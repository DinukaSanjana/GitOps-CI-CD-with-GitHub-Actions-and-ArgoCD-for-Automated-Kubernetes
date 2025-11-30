# Project: GitOps CI/CD with GitHub Actions and ArgoCD for Automated Kubernetes

**Repository:** https://github.com/DinukaSanjana/crud-app.git  
**Status:** Fully Working End-to-End GitOps Pipeline

## Overview
Fully automated GitOps CI/CD pipeline for a Node.js CRUD application deployed on AWS EKS.

- **CI** → GitHub Actions  
- **CD** → ArgoCD (Automatic Sync)  
- Git is the **single source of truth**

## Technologies Used
- AWS EKS (Kubernetes)
- Docker + Docker Hub
- GitHub Actions
- ArgoCD
- Node.js + MySQL (AWS RDS)

## Detailed Implementation Steps & Commands

### 1. Application Configuration
- Changed `app.js` to connect to created AWS RDS MySQL RDS database (host, user, password, database name updated)
- Changed `kubernetes/app.yaml` → updated Docker image to personal Docker Hub repository  
  `image: <your-username>/crud-app:latest`

### 2. GitHub Actions CI Setup
**Workflow file:** `.github/workflows/build.yaml`

**GitHub Actions performs:**
- Checkout code
- Build Docker image
- Tag image with commit SHA
- Push to Docker Hub
- Update `kubernetes/app.yaml` with new image tag
- Commit & push the updated manifest back to the same repo

**Required Secrets (Repository → Settings → Secrets and Variables → Actions):**
```text
DOCKER_HUB_USERNAME   → your dockerhub username
DOCKER_HUB_PASSWORD   → your dockerhub password or access token
GH_TOKEN              → GitHub Personal Access Token (Classic) with repo & workflow scope
```

**Test CI Pipeline:**
- Edited `README.md` (added a line)
- Committed → GitHub Actions triggered (yellow → green circle, sometimes red dot while running)
- Pipeline successfully built image, pushed to Docker Hub, updated `app.yaml`, and committed

### 3. AWS EKS Cluster Setup
- Created EKS cluster named `cluster-1`
-1`
- Created node group with 3 x t3.medium nodes
- Launched an EC2 instance (or used local machine)
- Installed AWS CLI and kubectl

```bash
aws configure
aws eks update-kubeconfig --region <region> --name cluster-1
kubectl get nodes
```
→ Confirmed 3 nodes in Ready state

### 4. ArgoCD Installation
```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

Verification:
```bash
kubectl get pods -n argocd
kubectl get svc -n argocd
```

Exposed ArgoCD UI:
```bash
kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'
# or
kubectl edit svc argocd-server -n argocd   # change type: ClusterIP → LoadBalancer
```

```bash
kubectl get svc -n argocd
```
Copied EXTERNAL-IP (Load Balancer URL) → opened in browser

### 5. ArgoCD Login
```bash
kubectl get secret -n argocd
kubectl get secret argocd-initial-admin-secret -n argocd -o jsonpath='{.data.password}' | base64 -d
```
Logged in with:
- Username: `admin`
- Password: (decoded from above command)

### 6. Create ArgoCD Application
In ArgoCD UI → New App with below settings:
- Application Name: `crud-app`
- Sync Policy: **Automatic**
- Repository URL: `https://github.com/DinukaSanjana/crud-app.git`
- Path: `kubernetes`
- Cluster URL: `https://kubernetes.default.svc`
- Namespace: `default`

Clicked **Create** → App synced and deployed successfully

### 7. Full GitOps CI/CD Flow Verification
1. Edited `README.md` again → committed and pushed
2. GitHub Actions triggered automatically
3. Pipeline built new Docker image → tagged with new commit SHA → pushed to Docker Hub
4. Updated `kubernetes/app.yaml` with new image tag → committed back
5. ArgoCD detected change in `app.yaml` within ~3 minutes
6. ArgoCD automatically synced cluster → rolling update performed
7. New version of app live without any manual kubectl apply

## Architecture Flow
```
Developer → GitHub (code + manifests)
     ↓ (push)
GitHub Actions → Build & Push Image → Update app.yaml → Commit
     ↓
ArgoCD watches Git → detects change → syncs EKS cluster automatically
     ↓
Kubernetes (EKS) → running latest image
```

## Outcome
- Zero-touch deployments – just git push
- Immutable Docker images tagged with commit SHA
- Full audit trail and instant rollback via Git
- Production-grade GitOps pattern implemented successfully

**Fully Automated CI/CD with GitOps**
```
