# 🚀 HelmStack: Kubernetes & Helm Deployment

## 🌟 Overview
Welcome to **HelmStack** – a streamlined Kubernetes deployment solution using Helm! This project is designed to help deploy a full application stack, including backend, frontend, and Redis, efficiently using Helm Charts.

## 🔥 Features
✅ Deploys **Backend, Frontend, and Redis** as Kubernetes Deployments.  
✅ Uses **Helm Charts** for easy deployment and version management.  
✅ Exposes services using **ClusterIP & NodePort**.  
✅ Defines **ConfigMaps** to pass environment variables.  
✅ Scalable and customizable with Helm `values.yaml`.  
✅ Secure deployment with internal communications restricted to ClusterIP.  
✅ Deployable with a **single Helm command**.  

## 📌 Deployment Details
### **Backend Service**
- Image: `shreybatra/sample-backend`
- Service Name: `backend-svc`
- Type: ClusterIP
- Port: `8000`
- Env Variable: `REDIS_URI=redis://redis-svc:6379`

### **Frontend Service**
- Image: `shreybatra/sample-frontend`
- Service Name: `frontend-svc`
- Type: NodePort
- Port: `5175`
- NodePort: `31000`
- Env Variable: `BACKEND_URL=http://backend-svc:8000`

### **Redis Service**
- Image: `redis`
- Service Name: `redis-svc`
- Type: ClusterIP
- Port: `6379`

## 🏗 Architecture Overview
[Frontend (LoadBalancer:5175)] → [Backend (ClusterIP:8000)] → [Redis (ClusterIP:6379)]

## 🎯 Installation & Setup
```sh
# Clone the Repository
git clone https://github.com/guptasamarth200/helmstack.git
cd helmstack
```
# Install Helm (if not installed)
```
curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 |bash
```
# Deploy the Application
```
helm install my-release.
```

# Verify the Deployment
```kubectl get pods```
```kubectl get services```
✅ Verification Steps
# Check if all pods are running
```kubectl get pods```

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

🔐 Security Considerations

All internal communications use ClusterIP services.
Frontend is exposed via LoadBalancer for internet access.
Backend and Redis remain internal.
Each component runs with minimum required privileges.
Environment variables are used for service discovery.


📈 Scaling

The application can be scaled by adjusting the replica count in values.yaml:
```yaml
frontend:
  replicas: 1  # Adjust as needed
backend:
  replicas: 1  # Adjust as needed
redis:
  replicas: 1  # Adjust as needed
```
Built with ❤️ for scalable and efficient Kubernetes deployments. 🚀
