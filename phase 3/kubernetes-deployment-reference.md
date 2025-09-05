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
