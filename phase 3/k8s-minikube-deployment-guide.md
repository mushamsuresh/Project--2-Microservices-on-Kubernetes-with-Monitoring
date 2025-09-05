# 🚀 Kubernetes Deployment Guide (Minikube)

This document provides a **step-by-step reference** for deploying a full-stack application (React frontend + Spring Boot backend) on **Kubernetes with Minikube**.

---

## 📂 Folder Structure
```
k8s-manifests/
 ├── backend-deployment.yaml
 ├── backend-service.yaml
 ├── frontend-deployment.yaml
 ├── frontend-service.yaml
 └── ingress.yaml
```

---

## 1️⃣ Backend (Spring Boot / Node / Flask)

### backend-deployment.yaml
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: backend-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: backend
  template:
    metadata:
      labels:
        app: backend
    spec:
      containers:
        - name: backend
          image: ghcr.io/your-username/springboot-backend:latest # replace with your image
          ports:
            - containerPort: 8080
```

### backend-service.yaml
```yaml
apiVersion: v1
kind: Service
metadata:
  name: backend-service
spec:
  selector:
    app: backend
  ports:
    - port: 8080
      targetPort: 8080
  type: ClusterIP
```

---

## 2️⃣ Frontend (React / Angular)

### frontend-deployment.yaml
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: frontend-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: frontend
  template:
    metadata:
      labels:
        app: frontend
    spec:
      containers:
        - name: frontend
          image: ghcr.io/your-username/address-ui:latest # replace with your image
          ports:
            - containerPort: 80
```

### frontend-service.yaml
```yaml
apiVersion: v1
kind: Service
metadata:
  name: frontend-service
spec:
  selector:
    app: frontend
  ports:
    - port: 80
      targetPort: 80
  type: ClusterIP
```

---

## 3️⃣ Ingress (Route frontend + backend)

### ingress.yaml
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: app-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /$1
spec:
  rules:
    - host: myapp.local   # fake hostname for local dev
      http:
        paths:
          - path: /?(.*)
            pathType: Prefix
            backend:
              service:
                name: frontend-service
                port:
                  number: 80
          - path: /api/?(.*)
            pathType: Prefix
            backend:
              service:
                name: backend-service
                port:
                  number: 8080
```

---

## 4️⃣ Deploying on Minikube

### Step 1: Start Minikube
```bash
minikube start
```

### Step 2: Enable Ingress Controller
```bash
minikube addons enable ingress
```

### Step 3: Namespace Setup
```bash
kubectl create namespace myapp
kubectl config set-context --current --namespace=myapp
```

### Step 4: Image Handling
- If **images are public on GHCR** → No secret needed
- If **images are private on GHCR**:
```bash
kubectl -n myapp create secret docker-registry ghcr-secret   --docker-server=ghcr.io   --docker-username=USERNAME   --docker-password=YOUR_GHCR_PAT   --docker-email=you@example.com
```
- If **images exist only locally**, load them:
```bash
minikube image load ghcr.io/mushamsuresh/springboot-backend:latest
minikube image load ghcr.io/mushamsuresh/address-ui:latest
```

### Step 5: Apply Manifests
```bash
mkdir -p k8s-manifests
kubectl apply -f k8s-manifests/
```

### Step 6: Verify Resources
```bash
kubectl get pods
kubectl get svc
kubectl get ingress
```

---

## 5️⃣ Accessing the App

### Option A: Hosts File Mapping
1. Get Minikube IP:
   ```bash
   minikube ip
   ```
2. Edit hosts file:
   - Linux/Mac → `/etc/hosts`
   - Windows → `C:\Windows\System32\drivers\etc\hosts`

   Add:
   ```
   192.168.49.2   myapp.local
   ```

3. Open in browser:
   - Frontend → `http://myapp.local`
   - Backend → `http://myapp.local/api`

### Option B: Port-Forward (Quick Testing)
Frontend:
```bash
kubectl -n myapp port-forward svc/frontend-service 8081:80
```
Open → http://localhost:8081

Backend:
```bash
kubectl -n myapp port-forward svc/backend-service 8082:8080
```
Open → http://localhost:8082

---

## 6️⃣ Debugging Steps

1. **Check backend service details**
   ```bash
   kubectl -n myapp get svc backend-service -o yaml
   ```
   Ensure ports are correct (8080 → 8080).

2. **Check backend pods health**
   ```bash
   kubectl -n myapp get pods -l app=backend
   kubectl -n myapp logs <backend-pod>
   ```
   Look for: `Tomcat started on port(s): 8080`.

3. **Re-run port-forward if needed**
   ```bash
   kubectl -n myapp port-forward svc/backend-service 8082:8080
   ```

---

## 7️⃣ What You Achieved ✅
- **Namespace**: created (`myapp`)
- **Deployments**: backend + frontend running
- **Pods**: healthy (Running, no restarts)
- **Services**: ClusterIP exposed
- **Ingress**: configured (myapp.local)
- **Port-forward**: verified backend & frontend
- **Spring Boot logs**: confirm correct port (8080)

---

## 8️⃣ Optional Next Steps
- Set up **Persistent Storage** (databases, volumes)
- Use **ConfigMaps & Secrets** for environment configs
- Test **scaling**:
  ```bash
  kubectl -n myapp scale deployment backend-deployment --replicas=3
  ```
- Add **CI/CD pipeline** for automatic deployments

---

## 9️⃣ Cleanup

### Delete only app resources
```bash
kubectl delete namespace myapp
```

### Reset whole Minikube cluster
```bash
minikube delete
minikube start
```
