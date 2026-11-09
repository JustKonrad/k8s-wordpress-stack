# WordPress and MariaDB on Kubernetes

A small project I built to get comfortable with the basic Kubernetes building blocks. It runs WordPress with a MariaDB database in its own namespace, with the database data kept on a persistent volume so it survives pod restarts. Everything is plain YAML, no Helm, so you can see exactly what each object does.

## How it fits together

- Everything lives in a `wordpress` namespace.
- MariaDB runs as a Deployment and is only reachable inside the cluster through a ClusterIP Service called `mariadb`.
- WordPress runs as a second Deployment and connects to the database using that service name, so I don't have to care about pod IPs.
- The database credentials are in a Secret. MariaDB loads all of it with `envFrom`, and WordPress picks the keys it needs with `secretKeyRef`.
- MariaDB stores its data on a 1Gi PersistentVolume (`hostPath` on the node, reclaim policy `Retain`), bound to a PVC by name.
- WordPress is exposed with a NodePort on `30007`.

## Files

| File | What it does |
|---|---|
| `namespace.yaml` | creates the `wordpress` namespace |
| `secret.yaml` | database name, user and passwords |
| `pv.yaml` / `pvc.yaml` | 1Gi volume for the MariaDB data |
| `mariadb-deployment.yaml` | MariaDB 10.5 using the secret and the PVC |
| `mariadb-service.yaml` | internal service so WordPress can reach the DB |
| `wordpress-deployment.yaml` | WordPress connected to MariaDB |
| `wordpress-service.yaml` | NodePort service on port 30007 |

## Running it

I tested it on a single-node cluster (Minikube). The values in `secret.yaml` are just examples, so change them before you deploy anything real.

Apply things in this order so the namespace and the storage exist before the pods need them:

```bash
kubectl apply -f namespace.yaml
kubectl apply -f secret.yaml
kubectl apply -f pv.yaml -f pvc.yaml
kubectl apply -f mariadb-deployment.yaml -f mariadb-service.yaml
kubectl apply -f wordpress-deployment.yaml -f wordpress-service.yaml
```

Check that everything is up:

```bash
kubectl get pods,svc,pvc -n wordpress
kubectl get pv
```

WordPress can restart once or twice at the start while MariaDB is still booting. That's expected, it settles as soon as the database accepts connections.

Then open `http://<node-ip>:30007`, or on Minikube:

```bash
minikube service wordpress -n wordpress
```

To check that the storage really works, delete the MariaDB pod and let the Deployment recreate it. Your WordPress site and posts should still be there:

```bash
kubectl delete pod -n wordpress -l app=mariadb
```

## What I'd change for a real setup

This is a learning project, so I kept it simple on purpose. A few things I know I'd do differently in production:

- **Secrets:** the passwords sit in plain text in the repo. I'd use Sealed Secrets or External Secrets instead and keep only an example file in Git.
- **Storage:** `hostPath` only works on one node. In a real cluster I'd use a StorageClass from the cloud provider and run MariaDB as a StatefulSet.
- **WordPress files:** only the database is persistent. Uploaded images and plugins in `wp-content` would be lost if the WordPress pod restarts, so it needs its own volume too.
- **Image versions:** WordPress uses the `latest` tag. I'd pin a version so a redeploy can't suddenly change things.
- **Health and resources:** no readiness/liveness probes or CPU/memory limits yet.
- **Exposure:** NodePort is fine for testing, but I'd put an Ingress with TLS in front of it.

Turning all of this into a Helm chart is the next step.
