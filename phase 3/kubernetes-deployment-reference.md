 📂 Folder Structure Suggestion

k8s-manifests/
 ├── backend-deployment.yaml
 ├── backend-service.yaml
 ├── frontend-deployment.yaml
 ├── frontend-service.yaml
 └── ingress.yaml
1. Backend Deployment + Service (Spring Boot / Node / Flask)
# k8s-manifests/backend-deployment.yaml
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
---
# k8s-manifests/backend-service.yaml
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

2. Frontend Deployment + Service (React / Angular)
# k8s-manifests/frontend-deployment.yaml
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
---
# k8s-manifests/frontend-service.yaml
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

3. Ingress (Route traffic → frontend + backend)
# k8s-manifests/ingress.yaml
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

🚀 Steps to Deploy in Minikube
# Start minikube
minikube start

# Enable ingress controller (for routing)
minikube addons enable ingress
(Optional) If your images are private on GHCR, create a pull secret (replace USERNAME and PAT):

kubectl create namespace myapp
kubectl -n myapp create secret docker-registry ghcr-secret \
  --docker-server=ghcr.io \
  --docker-username=USERNAME \
  --docker-password=YOUR_GHCR_PAT \
  --docker-email=you@example.com
kubectl config set-context --current --namespace=myapp
If your images are public, you can skip the secret.
If images exist only on your local machine, load them into Minikube:
minikube image load ghcr.io/mushamsuresh/springboot-backend:latest
minikube image load ghcr.io/mushamsuresh/address-ui:latest
kubectl create namespace myapp
kubectl config set-context --current --namespace=myapp
1) Create folder & files
 mkdir -p k8s-manifests

# Apply all manifests
kubectl apply -f k8s-manifests/

# Check pods and services
kubectl get pods
kubectl get svc
kubectl get ingress
🌐 Accessing Your App
Add an entry in /etc/hosts (Linux/Mac) or C:\Windows\System32\drivers\etc\hosts (Windows):
Option A — hosts file (easy & reliable)

Get Minikube IP:minikube ip
2. Edit your /etc/hosts file

Run:sudo nano /etc/hosts
Add a new line at the end:
192.168.49.2   myapp.local
Save and exit.
1. Verify backend service exists & is on the right port
kubectl -n myapp get svc backend-service -o yaml

Check that:ports:
- port: 8080
  targetPort: 8080
2. Verify backend pod is healthy
  kubectl -n myapp get pods -l app=backend
Then check logs of one pod:kubectl -n myapp logs <backend-pod-name>
Look for errors (port binding, database connection, etc.).
3. Double-check your port-forward command

You should be forwarding local 8082 → service 8080:
kubectl -n myapp port-forward svc/backend-service 8082:8080
If this runs, you should see:
Forwarding from 127.0.0.1:8082 -> 8080
Forwarding from [::1]:8082 -> 8080
Then http://localhost:8082 should hit your backend. 
Next debugging steps:
1. Check if your backend pods are actually running on 8080
kubectl -n myapp get pods -l app=backend
Pick one pod name and run: kubectl -n myapp logs <backend-pod-name>
Look for lines like:Tomcat started on port(s): 8080 (http)
If it says another port (like 8081, 5000, etc.), we’ll need to update the Service.
3. Re-run port-forward

Make sure you’re forwarding service 8080 → local 8082:
kubectl -n myapp port-forward svc/backend-service 8082:8080
✅ Your Spring Boot backend is listening on port 8080 inside the container.
So your Service definition is correct (targetPort: 8080).

That means the issue must be either:

the port-forward command

or something in the app (e.g. CORS / path mismatch).
1. Correct port-forward again

Run this (new terminal if you already killed the last one):
kubectl -n myapp port-forward svc/backend-service 8082:8080
🔎 What you’ve achieved so far

Namespace: ✅ created (myapp)

Deployments: ✅ running (backend-deployment, frontend-deployment)

Pods: ✅ healthy (all Running, no restarts)

Services: ✅ working (ClusterIP for backend & frontend)

Port-forwarding: ✅ verified access to both apps (localhost:8081 frontend, localhost:8082 backend)

Spring Boot logs: ✅ confirm app running on correct port (8080)

That means your core Kubernetes setup is successful 🎉
🔧 What’s still optional / next steps

Ingress: not yet working (so you’re using port-forward instead of http://myapp.local).

Persistent storage / DB: not set up (if your app needs it).

Secrets/ConfigMaps: you created image pull secret ✅, but if your app needs DB creds or configs, those can be externalized.

Scalability test: you could try scaling pods:
kubectl -n myapp scale deployment backend-deployment --replicas=3

