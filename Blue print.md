# 📌 Project 2: Non-AWS DevOps Project – Microservices on Kubernetes with Monitoring

## 🎯 Objective
Deploy a **microservices-based application** using Docker, Kubernetes, Prometheus, and CI/CD.

---

## 🛠️ Tech Stack
- **Linux** → Base environment for setup & scripting  
- **GitHub** → Code repo + GitHub Actions for CI/CD  
- **Shell Scripting** → Automate deployments, log rotation  
- **CI/CD** → GitHub Actions pipeline  
- **Containerization** → Docker (build microservices)  
- **Orchestration** → Kubernetes (EKS alternative: Minikube / Kind / local cluster)  
- **Monitoring** → Prometheus + Grafana dashboards  
- **IaC** → Terraform (provision K8s cluster, storage, networking)  

---

## 🔄 Workflow
1. **Developer commits code** → GitHub repo.  
2. **GitHub Actions pipeline**:  
   - Runs tests  
   - Builds Docker images  
   - Pushes to DockerHub  
   - Applies Kubernetes manifests (`kubectl apply`)  
3. **Kubernetes**:  
   - Deploys app microservices (frontend, backend, DB)  
   - Services + Ingress for routing  
4. **Prometheus**:  
   - Scrapes metrics from microservices + Node Exporter  
5. **Grafana**:  
   - Visualizes metrics (CPU, memory, requests)  

---

## 📊 Deliverables
- ✅ Multi-service app running on Kubernetes  
- ✅ GitHub Actions CI/CD pipeline  
- ✅ Terraform configs for infra setup  
- ✅ Prometheus + Grafana dashboards for monitoring  

---

✅ With these two projects, you cover **AWS-native DevOps** and **non-AWS DevOps (open-source stack)** — great for interviews and portfolio.
