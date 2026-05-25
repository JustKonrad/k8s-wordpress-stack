# k8s-wordpress-stack

Kubernetes WordPress stack using Deployments, Services, Persistent Volumes and MariaDB.

---

## Architecture

This project deploys a WordPress application on Kubernetes using:

- Deployments
- Services
- Persistent Volumes
- Persistent Volume Claims
- Kubernetes Secrets
- MariaDB backend

---

## Technologies

- Kubernetes
- Docker
- YAML
- WordPress
- MariaDB
- Persistent Storage

---

## Project Structure

```bash
.
├── namespace.yaml
├── secret.yaml
├── pv.yaml
├── pvc.yaml
├── mariadb-deployment.yaml
├── mariadb-service.yaml
├── wordpress-deployment.yaml
├── wordpress-service.yaml
└── README.md
```

---

## Deployment Steps

### Create namespace

```bash
kubectl apply -f namespace.yaml
```

### Create storage

```bash
kubectl apply -f pv.yaml
kubectl apply -f pvc.yaml
```

### Create secrets

```bash
kubectl apply -f secret.yaml
```

### Deploy MariaDB

```bash
kubectl apply -f mariadb-deployment.yaml
kubectl apply -f mariadb-service.yaml
```

### Deploy WordPress

```bash
kubectl apply -f wordpress-deployment.yaml
kubectl apply -f wordpress-service.yaml
```

---

## Verify Resources

Check running pods:

```bash
kubectl get pods
```

Check services:

```bash
kubectl get svc
```

Check persistent volumes:

```bash
kubectl get pv
kubectl get pvc
```

---

## Features

- WordPress deployment on Kubernetes
- MariaDB backend database
- Persistent storage support
- Kubernetes Secrets management
- Internal service communication
- Namespace isolation

---

## What I Learned

- Kubernetes Deployments
- Kubernetes Services
- Persistent Volumes and PVCs
- Secrets management
- Containerized WordPress architecture
- Multi-service application deployment
- Kubernetes resource management

---

## Future Improvements

- Add Ingress Controller
- Add HTTPS with TLS
- Deploy with Helm
- Add Horizontal Pod Autoscaler
- CI/CD pipeline integration

---

## Author

Konrad Sagan  
Junior DevOps & Cloud Engineer
