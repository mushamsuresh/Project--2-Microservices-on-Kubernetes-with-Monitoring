# 🏗️ Project 2: Non-AWS DevOps Project – Step-by-Step Guide

## **Phase 1: Environment Setup**
1. **Linux machine** (Ubuntu / WSL / VM / Cloud VM).  
2. Install tools:  
   - `docker` & `docker-compose`  
   - `kubectl`  
   - `minikube` or `kind` (to run Kubernetes locally)  
   - `helm` (package manager for K8s apps)  
   - `terraform`  
   - `git` + GitHub account  
   - Prometheus & Grafana (via Helm later)  

✅ Outcome: A local dev environment ready for DevOps.

---

## **Phase 2: Application (Microservices)**
Pick a **sample microservices app** (to avoid spending too much time coding). Options:
- A simple **To-Do app** with:
  - Frontend (React/Angular)  
  - Backend API (Node.js / Python Flask / Spring Boot)  
  - Database (Postgres/MySQL/MongoDB in K8s)  

👉 Keep it simple but realistic.

- Create **Dockerfiles** for each microservice.  
- Test locally using `docker-compose up`.  

✅ Outcome: Your app works locally with Docker.

---

## **Phase 3: Kubernetes Deployment**
1. Write **Kubernetes manifests**:
   - `Deployment` for frontend, backend, DB  
   - `Service` for internal/external communication  
   - `Ingress` for routing traffic to frontend/backend  

2. Deploy to **Minikube**:  
   ```bash
   minikube start
   kubectl apply -f k8s-manifests/
   kubectl get pods,svc
   ```

✅ Outcome: App is running inside Kubernetes.

---

## **Phase 4: Monitoring Setup**
1. Install **Prometheus & Grafana** with Helm:
   ```bash
   helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
   helm install prometheus prometheus-community/kube-prometheus-stack
   ```

2. Configure **Prometheus** to scrape:  
   - Node Exporter (system metrics)  
   - Your backend microservice (expose `/metrics`)  

3. Access **Grafana dashboard**:
   ```bash
   kubectl port-forward svc/prometheus-grafana 3000:80
   ```
   Login: `admin/admin`

✅ Outcome: Monitoring dashboards running.

---

## **Phase 5: CI/CD Pipeline (GitHub Actions)**
1. Create `.github/workflows/ci-cd.yml`:
   - Step 1: Run tests  
   - Step 2: Build & push Docker images → DockerHub  
   - Step 3: Deploy to Kubernetes (`kubectl apply`)  

2. Store secrets:
   - `DOCKERHUB_USERNAME`  
   - `DOCKERHUB_TOKEN`  
   - `KUBECONFIG` (if needed for remote cluster)  

✅ Outcome: On every commit → pipeline runs → new version deployed.

---

## **Phase 6: Infrastructure as Code (Terraform)**
1. Write Terraform configs for:
   - Kubernetes cluster (if using Kind/Minikube, this can be optional)  
   - Storage (PVCs for DB)  
   - Networking (Ingress + LoadBalancer)  

2. Example:
   ```hcl
   resource "kubernetes_deployment" "backend" {
     metadata {
       name = "backend"
     }
     spec {
       replicas = 2
       template {
         spec {
           container {
             image = "your-dockerhub/backend:latest"
           }
         }
       }
     }
   }
   ```

✅ Outcome: Infra is managed by code (IaC).

---

# 🎯 Final Project Flow
1. Dev pushes code → GitHub Actions triggers  
2. CI/CD → Docker build + push  
3. CD → Deploys to Kubernetes  
4. Prometheus scrapes metrics → Grafana dashboards show app health  

---

👉 Suggestion: Start small — get **Phase 2 (Docker + app)** working first. Then move to Kubernetes, then CI/CD, then monitoring.
