# Kubernetes Course – Task 2

## FastAPI + MySQL with ConfigMap, Secret, Persistent Storage & Ingress

### Overview

This project deploys a FastAPI Guestbook application connected to a MySQL 8.4 database on a three-node Kubernetes cluster using Minikube.

The application supports reading and writing messages through a REST API. MySQL data is persisted using Kubernetes PersistentVolumes and PersistentVolumeClaims.

### Architecture

**Request flow:**

Browser → NGINX Ingress Controller → webapp-ingress → webapp-api Service → 2 FastAPI Pods → mysql Service → MySQL Pod → Persistent Storage

### Kubernetes Resources

| Resource | Name | Description |
|---|---|---|
| Namespace | webapp-stack | Isolates application resources |
| ConfigMap | webapp-config | Application configuration |
| Secret | mysql-secret | Database credentials |
| PersistentVolume | mysql-pv | 2Gi hostPath storage |
| PersistentVolumeClaim | mysql-pvc | Requests persistent storage |
| Deployment | mysql | Runs one MySQL replica |
| Deployment | webapp-api | Runs two FastAPI replicas |
| Service | mysql | Internal database access on port 3306 |
| Service | webapp-api | Exposes FastAPI on port 80 |
| Ingress | webapp-ingress | Routes HTTP traffic to FastAPI |

### Docker Images

The provided application source files were built into two Docker images:

- `webapp-api:task2`
- `webapp-mysql:task2`

Both images were loaded into the Minikube environment using `minikube image load`.

### Persistent Storage

MySQL uses a 2Gi PersistentVolume configured with:

- StorageClass: `manual`
- Access mode: `ReadWriteOnce`
- Reclaim policy: `Retain`
- Host path: `/data/webapp-mysql`
- Node affinity: `minikube-m02`

The PersistentVolumeClaim is mounted at `/var/lib/mysql` inside the MySQL container.

Node affinity ensures that MySQL runs on the node containing its data.

### Application Configuration

The ConfigMap provides application settings including `APP_TITLE`, `APP_ENV`, `MYSQL_HOST`, `MYSQL_PORT`, and `MYSQL_DATABASE`.

The Secret provides database credentials to MySQL and FastAPI.

### Health Checks

Both deployments include Kubernetes readiness and liveness probes.

- FastAPI uses the `/health` HTTP endpoint.
- MySQL uses `mysqladmin ping`.
- The MySQL deployment uses the `Recreate` update strategy.

### Verification

The following functionality was tested:

- Three Kubernetes nodes running.
- MySQL deployment ready with one replica.
- FastAPI deployment ready with two replicas.
- PersistentVolume and PersistentVolumeClaim bound.
- MySQL scheduled on `minikube-m02`.
- Ingress configured with NGINX.
- FastAPI accessible through the Ingress Controller.
- Application information retrieved using `/api/info`.
- Messages retrieved using `GET /api/messages`.
- New messages stored using `POST /api/messages`.
- MySQL Pod deleted and recreated successfully.
- Previously stored messages remained available after the Pod replacement.

### API Endpoints

| Method | Endpoint | Description |
|---|---|---|
| GET | `/` | Guestbook home page |
| GET | `/health` | Application health check |
| GET | `/api/info` | Application and database information |
| GET | `/api/messages` | Retrieve stored messages |
| POST | `/api/messages` | Create a new message |

### Key Learnings

This task demonstrates:

- Building and loading local Docker images into Minikube.
- Managing configuration using ConfigMaps and Secrets.
- Persisting database data with PV and PVC.
- Scheduling storage-dependent workloads using node affinity.
- Deploying multiple application replicas.
- Service discovery through Kubernetes DNS.
- HTTP routing using NGINX Ingress.
- Monitoring container health with Kubernetes probes.
- Validating data persistence after Pod replacement.

### Screenshots

Execution screenshots and verification evidence are available in the `screenshots/` directory.



#### Execution Evidence

![01-nodes.png](<screenshots/01-nodes.png>)

![02-images.png](<screenshots/02-images.png>)

![03-config-secret.png](<screenshots/03-config-secret.png>)

![04-storage.png](<screenshots/04-storage.png>)

![05-deployments.png](<screenshots/05-deployments.png>)

![06-pods.png](<screenshots/06-pods.png>)

![07-ingress.png](<screenshots/07-ingress.png>)

![08-api-info.png](<screenshots/08-api-info.png>)

