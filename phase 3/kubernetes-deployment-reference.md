# Kubernetes Deployment Reference Guide

This document summarizes the successful steps taken to deploy and run a **Spring Boot backend** and **React frontend** on Kubernetes using Minikube.

---

## ✅ Steps Completed

### 1. Start Minikube
```bash
minikube start
```

### 2. Create Namespace
```bash
kubectl create namespace myapp
```
Namespace `myapp` is now used for all resources.

### 3. Configure kubectl Context
```bash
kubectl config set-context --current --namespace=myapp
```

### 4. Push Images to GitHub Container Registry (GHCR)
- Backend: `ghcr.io/mushamsuresh/springboot-backend:latest`
- Frontend: `ghcr.io/mushamsuresh/address-ui:latest`

### 5. Load Images into Minikube
```bash
minikube image load ghcr.io/mushamsuresh/springboot-backend:latest
minikube image load ghcr.io/mushamsuresh/address-ui:latest
```

### 6. Create Docker Registry Secret
```bash
kubectl create secret docker-registry ghcr-secret   --docker-server=ghcr.io   --docker-username=mushamsuresh   --docker-password=<YOUR_GHCR_PAT>   --docker-email=sureshmusham51@gmail.com
```

Patch the default service account to always use this secret:
```bash
kubectl patch serviceaccount default   -p '{"imagePullSecrets": [{"name": "ghcr-secret"}]}'
```

### 7. Deploy Backend (Spring Boot)
Created **backend.yaml** with Deployment + Service:
- Deployment: `backend-deployment`
- Service: `backend-service` (ClusterIP, port 8080)

### 8. Deploy Frontend (React)
Created **frontend.yaml** with Deployment + Service:
- Deployment: `frontend-deployment`
- Service: `frontend-service` (ClusterIP, port 80)

### 9. Verify Deployments & Services
```bash
kubectl -n myapp get pods,svc
```
✅ Both backend and frontend pods are **Running**.  
✅ Services are created (`backend-service` and `frontend-service`).

### 10. Confirm Backend Logs
```bash
kubectl -n myapp logs <backend-pod>
```
Output confirmed:
```
Tomcat started on port 8080 (http) with context path '/'
```

### 11. Access Services with Port-Forward
Frontend:
```bash
kubectl -n myapp port-forward svc/frontend-service 8081:80
# Access at http://localhost:8081
```
Backend:
```bash
kubectl -n myapp port-forward svc/backend-service 8082:8080
# Access at http://localhost:8082/api
```

✅ Verified both applications are accessible locally.

---

## 🎯 Current Status
- Backend and frontend successfully deployed on Kubernetes.  
- Pods and services are working correctly.  
- Applications accessible via port-forwarding.  

---

## 🚀 Next Steps (Optional)
- Configure **Ingress** with NGINX to access via `http://myapp.local`.  
- Set up **Persistent Volumes** if the backend needs a database.  
- Add **ConfigMaps/Secrets** for application configs.  
- Explore **scaling** deployments:
  ```bash
  kubectl -n myapp scale deployment backend-deployment --replicas=3
  ```

---

## ✅ Conclusion
Your Kubernetes deployment is **successful** — you now have a working setup for both frontend and backend running inside Minikube.

