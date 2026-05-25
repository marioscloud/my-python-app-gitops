# 🚀 Production-Grade GitOps CI/CD Pipeline with Blue-Green Deployments

![Build Status](https://img.shields.io/badge/build-passing-brightgreen)
![Infrastructure](https://img.shields.io/badge/Infrastructure-Terraform-623CE4)
![Kubernetes](https://img.shields.io/badge/Kubernetes-GKE-326CE5)
![GitOps](https://img.shields.io/badge/GitOps-ArgoCD-EF7B4D)
![CI](https://img.shields.io/badge/CI-GitHub_Actions-2088FF)

## 📌 Overview
This project demonstrates a fully automated, production-ready **GitOps architecture** deployed on **Google Kubernetes Engine (GKE)**. It establishes a strict separation of concerns across infrastructure provisioning, application development, and cluster deployment state. The architecture utilizes **Argo CD** and **Argo Rollouts** to achieve seamless, **Zero-Downtime Blue-Green Deployments**.

Designed following modern Cloud-Native and SRE best practices, this ecosystem ensures immutable artifact promotion, infrastructure as code (IaC) modularity, and automated drift reconciliation.

---

## 🏗️ Architecture & Multi-Repository Structure
To adhere strictly to organizational GitOps and security principles, the system is decoupled into three specialized repositories:

1. **[Source Code Repository (my-python-app)](https://github.com/marioscloud/my-python-app)**: Houses the Python (Flask) microservice application logic, Docker container definitions, and the Continuous Integration (CI) pipeline via GitHub Actions.
2. **[GitOps / Cluster State Repository (my-python-app-gitops)](https://github.com/marioscloud/my-python-app-gitops)**: Acts as the absolute "Source of Truth" for the Kubernetes runtime environment. Contains Helm charts, application manifests, and declarative Argo Rollout deployment strategies.
3. **[Cloud Infrastructure Repository (my-python-app-infra)](https://github.com/marioscloud/my-python-app-infra)**: Contains modular HashiCorp Terraform configurations dedicated to provisioning production-grade, secure GKE clusters, VPC networking, and cloud IAM roles within Google Cloud Platform (GCP).

### 🔄 The Automated CI/CD Lifecycle Flow
1. **Code Commit:** Developer merges a feature or fix into the `master` branch of the **Source Code** repository.
2. **Continuous Integration (CI):** GitHub Actions triggers a build execution, verifies tests, containerizes the application, and tags the artifact using an immutable Git commit SHA.
3. **Artifact Registry:** The secure image is pushed automatically to Docker Hub.
4. **GitOps Manifest Automation:** The CI pipeline executes an automated commit back to the **GitOps Repository**, updating the image `tag` key inside `values.yaml` with the precise commit SHA.
5. **Continuous Deployment (CD):** Argo CD detects the configuration drift between Git and GKE, initiating an automated state synchronization.
6. **Zero-Downtime Promotion:** Argo Rollouts orchestrates the Blue-Green progression—initializing the "Green" infrastructure, executing target health validation, and cleanly executing the traffic cutover without losing a single packet.

---

## 🛠️ Technology Stack & Competencies Demonstrated

* **Cloud Provider:** Google Cloud Platform (GCP)
* **Infrastructure as Code (IaC):** HashiCorp Terraform (Modular GKE, VPC, IAM provisioning)
* **Container Orchestration:** Kubernetes (GKE)
* **Continuous Integration (CI):** GitHub Actions
* **Continuous Deployment (CD) / GitOps:** Argo CD
* **Advanced Deployment Strategies:** Argo Rollouts (Blue-Green traffic management)
* **Containerization:** Docker & Docker Hub
* **Microservices Framework:** Python / Flask

---

## 🌟 Key DevOps Principles Highlighted

* **Strict Multi-Repo Separation of Concerns:** Separating foundational cloud fabric (Terraform) from target cluster application state (GitOps Helm manifests) isolates the blast radius and enforces structured IAM boundaries.
* **Declarative Truth & Self-Healing:** The active Kubernetes cluster state is fully version-controlled. Any manual hotfixes or configuration drifts on the live cluster are dynamically overwritten and reconciled by Argo CD.
* **Immutable Deployments:** Eliminating unstable "latest" tag practices by using `github.sha` mappings guarantees auditable, predictable tracing from container runtime directly back to the original source code commit.
* **Decoupled Access Control:** The CI server lacks cluster administrative keys (`kubeconfig`). It only interacts with Git text mutations, shielding the cluster from exposure if the CI pipeline runner is compromised.

---

## 🚀 Deployment & Replication Strategy

### 1. Provision Multi-Zone GKE Cloud Infrastructure
Initialize and apply the foundational infrastructure via the dedicated IaC repository:
```bash
cd my-python-app-infra/terraform
terraform init
terraform apply -auto-approve
```

### 2. Initialize GitOps Controllers
Establish the GitOps execution engine inside the freshly created cluster:
```bash
kubectl create namespace argocd
kubectl apply -n argocd -f [https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml](https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml)
```

### 3. Register GitOps Manifest Target
Bind your declarative application runtime configurations to Argo CD:
```bash
argocd app create mastering-blue-green \
  --repo [https://github.com/marioscloud/my-python-app-gitops.git](https://github.com/marioscloud/my-python-app-gitops.git) \
  --path . \
  --dest-server [https://kubernetes.default.svc](https://kubernetes.default.svc) \
  --dest-namespace default \
  --sync-policy automated
```

### 4. Continuous Delivery Execution
Modify application behavior inside `app.py` within the **Source Code Repository** and push to `master`. The infrastructure automatically triggers artifact generation, updates target cluster metadata, and runs safe Blue-Green environment migrations dynamically.

---
*Mario Araos. Devops Engineer | CKA | LFCS | Maintained to showcase technical proficiency in Cloud Architecture, Continuous Delivery systems, and Site Reliability Engineering (SRE).*
