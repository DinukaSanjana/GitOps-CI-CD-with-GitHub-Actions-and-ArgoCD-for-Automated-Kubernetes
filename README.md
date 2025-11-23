# Project : GitOps CI-CD with GitHub Actions and ArgoCD for Automated Kubernetes

## Repo
https://github.com/DinukaSanjana/crud-app.git

## Project Purposes
- Build a fully automated CI/CD system using GitOps principles.
- Git as the single source of truth.
- Automated CI/CD with GitHub Actions for CI and Argo CD for CD.
- Seamless Image Versioning.
- Continuous Delivery.
- Traceability and Rollbacks.

## Using Technologies
- GitHub Actions (CI Tool): Continuous Integration (CI).
  - Code checkout.
  - Application build.
  - Docker image build with commit hash tag.
  - Image push to remote registry.
  - Kubernetes manifest update and commit.
- Argo CD (GitOps CD Tool): Continuous Delivery (CD).
  - GitOps tool.
  - Monitors Git repository.
  - Syncs cluster to desired state.
- Kubernetes (AWS EKS): Platform for containerized application deployment.
- Docker: Containerize Node.js application.
- Node.js (JavaScript): CRUD App.
- MySQL (AWS RDS): Database outside Kubernetes cluster.

## Lessons of the Project
1. Lesson 1: The Application
   - Simple Node.js CRUD (Create, Read, Update, Delete) application.
   - Dependencies: express and mysql.
   - Database: Testdb-one on AWS RDS MySQL.
   - Dockerfile: base image node:14, expose port 3000, npm start command.
   - Kubernetes Manifests: kubernetes/app.yaml (Deployment and Service configuration).

2. Lesson 2: GitHub Actions CI
   - Workflow File: .github/workflows/build.yaml.
   - Trigger: push event to main branch.
   - Steps:
     1. Login to Docker Hub: Using DOCKER_HUB_USERNAME and DOCKER_HUB_PASSWORD.
     2. Extract Commit Hash: git rev-parse --short HEAD.
     3. Build and Push Image: Build with commit hash tag, push to Docker Hub.
     4. Update Manifest: sed command on kubernetes/app.yaml to change image tag.
     5. Commit Changes: Commit and push updated app.yaml using GH_TOKEN.
   - Secrets: DOCKER_HUB_PASSWORD, DOCKER_HUB_USERNAME, GH_TOKEN.

3. Lesson 3: EKS Setup
   - Cluster: AWS EKS cluster-hyphen-one with t3.medium nodes (3 nodes).
   - Local Connection:
     1. Install aws cli and kubectl.
     2. aws eks update-kubeconfig --region ap-south-1 --name cluster-hyphen-one.
     3. kubectl get nodes.

4. Lesson 4: Argo CD Installation
   - Namespace: kubectl create namespace argocd.
   - Installation: kubectl apply -f install.yaml from official documentation.
   - Expose UI:
     1. argo-cd-server service default type ClusterIP.
     2. kubectl edit svc argo-cd-server -n argocd, change type to LoadBalancer.
     3. External Load Balancer URL for Argo CD UI.

5. Lesson 5: Argo Setup & Deploy
   - Login:
     1. Username: admin.
     2. Password from argocd-initial-admin-secret (Base64 encoded).
     3. kubectl get secret argocd-initial-admin-secret -n argocd -o yaml, base64 -d.
   - Create Application (in Argo UI):
     1. New Application.
     2. Application Name: crud-app.
     3. Sync Policy: Automatic.
     4. Repository URL: GitHub repo HTTPS URL.
     5. Path: kubernetes.
     6. Destination: Cluster URL kubernetes.default.svc, Namespace default.
   - Initial Sync: Creates Deployment and Service from kubernetes/app.yaml.

6. Lesson 6: The Full GitOps Loop
   - App Verification: my-app-service LoadBalancer URL, test CRUD operations.
   - The Test:
     1. Edit README.md (add text "Change"), push.
     2. CI Triggers: GitHub Actions pipeline runs.
     3. Build new Docker image with new commit hash, push to Docker Hub.
     4. Update kubernetes/app.yaml with new hash, commit to Git.
     5. CD Triggers: Argo CD detects change (default 3 minutes).
     6. Auto-Sync: Updates deployment to new image (rolling update).
     7. Application updated in cluster without manual intervention.

## Project Outcomes
- Automated CI/CD pipeline using GitOps principles for efficient Kubernetes deployments.
- Seamless image versioning and updates managed through GitHub Actions and DockerHub.
- Continuous delivery achieved via ArgoCD, ensuring up-to-date applications in the cluster.
- Improved traceability, rollback capabilities, and operational efficiency.

## Practical Sessions
- Change the app.js file to match to created RDS sql database.
- Change the app.yaml (crud-app/kubernetes/app.yaml) file by adding our image name with the username/image name from Docker Hub.
- CI in Action
  - Go to Repository → settings → Secrets and Variables → Actions → New Repository Secret
  - Create the 3 Secrets
    - DOCKER_HUB_USERNAME: ddinuka [Add secret]
    - DOCKER_HUB_PASSWORD: Abcd@1234 [Add secret]
    - GH_TOKEN: <Paste here the token>
      - GitHub Account → Settings → Developer Settings → Personal access tokens → Tokens (classic) → Generate new token → Generate new token (Classic)
      - Note: githubactions-personal-action-token
      - Expiration: 7 Days
      - Select scopes: Workflows, repo, write packages, delete packages
      - [Generate token] [Copy the Token] [Add secret]
- Let test our pipeline is working or not?
  - Edit the README.md (Add a text)
  - [Commit Changes]
  - There shows a pending opting with red dot. Click on that and go to details.
- EKS and kubectl setup
  - create a cluster as “cluster-1”
  - Upgrade policy: Standard
  - Create a node group with 3 nodes as “1nq” t3.medium
  - Install AWS CLI and kubectl
  - Connect eks to a EC2 instance
  - aws eks update-kubeconfig --region ap-south-1 --name cluster-1
  - kubectl get nodes
- Argo Install
  - Use official Argocd documentation
  - kubectl get pods -n argocd
  - kubectl get svc -n argocd
  - Edit the argocd-server Type as “ClusterIP” to “NodePort” or “Load Balancer”
  - kubectl edit svc argocd-server -n argocd
  - Change the type as “ClusterIP” to “LoadBalancer”
  - kubectl get svc -n argocd
  - Take Load Balancer End Point and paste on a browser
  - There not loaded page
  - Go to EC2 → Load Balancer
  - There load balancer has not registered for any instance
  - This all instance should be in service (not appear 0 of 3 instances in service)
  - After reload the tab
- Argo Setup and Install
  - kubectl get secret -n argocd
  - kubectl get secret argocd-initial-admin-secret -n argocd -o yaml
  - echo “<Password>” | base64 -d
  - Copy that password
  - Paste that on the argocd web page password section, And Username is the admin
  - Username: admin
  - password: <Converted password>
  - [Signing In]
  - Click “Create Application” section
  - crud-app
  - default
  - Sync policy: Automatic
  - Repository URL: <Paste our GitHub repo URL>
  - Path: kubernetes
  - Cluster URL: select (https://kubernetes.default.svc)
  - Namespace: default
  - [Create]
  - Click on that
  - kubectl get svc -n argocd
  - kubectl get svc
  - Copy the my-app-service → LoadBalancer endpoint and paste on a browser
  - Then change the README.md file and “commit”
  - Pipeline will trigger
  - Click on job details
  - YAML manifest file also recreate
  - Dockerhub has a new image pushed
  - Check the argocd
  - Argocd takes 3 minutes to update
  - Click on refresh
  - There shows processing on argocd
  - Status shows Healthy
  - Again Update the Database
  - Finished ……..!!!!!!!!!
