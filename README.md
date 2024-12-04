# Cosmocloud Deploy Helm Chart

This Helm chart deploys a complete application stack consisting of a frontend, backend, and Redis database.

Author: Samarth Gupta

## Components

1. **Frontend**
   - Image: `shreybatra/sample-frontend`
   - Exposed via LoadBalancer service on port 5175
   - Connects to backend service
   - Environment Variables:
     - BACKEND_URL: Points to backend service

2. **Backend**
   - Image: `shreybatra/sample-backend`
   - Internal ClusterIP service on port 8000
   - Connects to Redis service
   - Environment Variables:
     - REDIS_URI: Points to Redis service

3. **Redis**
   - Image: `redis`
   - Internal ClusterIP service on port 6379
   - Used as database

## Architecture Overview
```
[Frontend (LoadBalancer:5175)] → [Backend (ClusterIP:8000)] → [Redis (ClusterIP:6379)]
```

## Prerequisites

- Kubernetes cluster (1.16+)
- Helm 3.x installed
- kubectl configured to communicate with your cluster

## Installation

```bash
# Clone the repository
git clone <repository-url>
cd cosmocloud-deploy

# Install the chart
helm install my-release .

# Verify the deployment
kubectl get pods
kubectl get services
```

## Verification Steps

1. Check if all pods are running:
```bash
kubectl get pods
```
Expected output:
```
NAME                        READY   STATUS    RESTARTS   AGE
frontend-xxxxxx-xxxx       1/1     Running   0          1m
backend-xxxxxx-xxxx        1/1     Running   0          1m
redis-xxxxxx-xxxx          1/1     Running   0          1m
```

2. Verify services are created:
```bash
kubectl get services
```
Expected output:
```
NAME          TYPE           CLUSTER-IP       EXTERNAL-IP     PORT(S)          AGE
frontend-svc  LoadBalancer   10.xxx.xxx.xxx   <external-ip>   5175:31000/TCP   1m
backend-svc   ClusterIP      10.xxx.xxx.xxx   <none>          8000/TCP         1m
redis-svc     ClusterIP      10.xxx.xxx.xxx   <none>          6379/TCP         1m
```

3. Access the frontend:
   - Get the external IP: `kubectl get svc frontend-svc`
   - URL: `http://<external-ip>:5175`

4. Check pod logs:
```bash
kubectl logs -l app=frontend
kubectl logs -l app=backend
kubectl logs -l app=redis
```

## Configuration

The following table lists the configurable parameters in `values.yaml`:

| Parameter | Description | Default |
|-----------|-------------|---------|
| `backend.replicas` | Number of backend replicas | `1` |
| `backend.image.repository` | Backend image repository | `shreybatra/sample-backend` |
| `backend.image.tag` | Backend image tag | `latest` |
| `backend.service.port` | Backend service port | `8000` |
| `frontend.replicas` | Number of frontend replicas | `1` |
| `frontend.image.repository` | Frontend image repository | `shreybatra/sample-frontend` |
| `frontend.image.tag` | Frontend image tag | `latest` |
| `frontend.service.port` | Frontend service port | `5175` |
| `redis.replicas` | Number of Redis replicas | `1` |
| `redis.image.repository` | Redis image repository | `redis` |
| `redis.image.tag` | Redis image tag | `latest` |
| `redis.service.port` | Redis service port | `6379` |

## Security Considerations

- All internal communications use ClusterIP services
- Frontend is exposed via LoadBalancer for internet access
- Backend and Redis remain internal
- Each component runs with minimum required privileges
- Environment variables are used for service discovery

## Scaling

The application can be scaled by adjusting the replica count in `values.yaml`:
```yaml
frontend:
  replicas: 1  # Adjust as needed
backend:
  replicas: 1  # Adjust as needed
redis:
  replicas: 1  # Adjust as needed
```