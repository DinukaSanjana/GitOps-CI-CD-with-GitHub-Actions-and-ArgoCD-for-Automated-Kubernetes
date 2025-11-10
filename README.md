change 
# DevOps Mentor Task

## Presentation Video

https://github.com/user-attachments/assets/cc52304e-0107-46f6-96e3-c20b7738408b

## Architecture Diagrams

### CI/CD Pipeline Diagram

![arch2](https://github.com/user-attachments/assets/84e7501b-e925-455c-8674-6e343225fd68)

### Terraform and AWS Resources Diagram

![arch1](https://github.com/user-attachments/assets/71f63fb4-f611-459e-93bb-6cd232a5771a)

## Dockerizing the Node.js App

The Dockerfile for the Node.js app is included in the repository.

## Setting Up AWS Resources

The following AWS resources are set up using Terraform:

1. **EC2 Instances**:
   - `nodejsapp`
   - `jenkins`
   - `monitoring`

2. **RDS Instance**:
   - mysql
 
3. **Key Pairs**:
   - `jenkins`
   - `nodejsapp`

4. **Security Groups**:
   - `jenkins`
   - `nodejsapp`
   - `rds`
   - `monitoring`
  
## Terraform Templates

The Terraform templates provision the required infrastructure on AWS. The following files are included in the repository:

- `backend.tf`: Configures the backend for Terraform state storage.
- `instances.tf`: Defines the EC2 instances for Jenkins, monitoring, and the Node.js app.
- `keypairs.tf`: Defines the key pairs for SSH access to the instances.
- `providers.tf`: Configures the providers required for the AWS resources.
- `rds.tf`: Defines the RDS instance for the MySQL database.
- `secgrp.tf`: Defines the security groups for Jenkins, monitoring, Node.js app, and RDS.
- `vars.tf`: Defines the variables used in the Terraform configuration.

## Monitoring

Monitoring is set up using Prometheus and Grafana. These services are deployed on a separate EC2 instance.

1. **Prometheus Configuration**:
   - Scrape configurations for the Node.js app and other services are added to \`prometheus.yml\`.

2. **Grafana Configuration**:
   - Prometheus is added as a data source in Grafana.
   - Dashboards are created to visualize key metrics.

## Setting Up CI/CD Pipeline

The Jenkinsfile for setting up the CI/CD pipeline is included in the repository.  

## Setup and Run Instructions

### Step-by-Step Process

1. **Created an S3 Bucket for Terraform State File**:
   - I started by creating an S3 bucket to store the Terraform state file. This bucket is essential for managing the state of the Terraform deployment and ensuring consistent provisioning of resources.

2. **Provisioned Resources Using Terraform**:
   - I used Terraform from my local computer to provision the necessary AWS resources. This involved:
     - Initializing Terraform with `terraform init`.
     - Validating the configuration with `terraform validate`.
     - Planning the deployment with `terraform plan`.
     - Applying the configuration with `terraform apply`.

3. **Configured Each Instance**:

   - **Jenkins Server**:
     - I installed Jenkins on the designated server.
     - I added the required plugins, to facilitate secure operations.
     - I configured credentials within Jenkins, including the GitHub access token, Docker credentials, and SSH access to EC2.
     - I created a Jenkins pipeline and prepared it for the CI/CD process.
     - I installed Docker on the Jenkins instance to handle containerization tasks.

   - **Node.js Instance**:
     - I installed Docker on the Node.js instance to support containerized applications.
     - I set up Node Exporter to collect and expose metrics for monitoring purposes.

   - **Monitoring Instance**:
     - I set up Prometheus on the monitoring instance to scrape and store metrics from various sources.
     - I installed and configured Grafana to visualize the metrics collected by Prometheus.

4. **Created a Webhook on GitHub**:
   - To automate the build process, I created a webhook on the GitHub repository. This webhook triggers the Jenkins pipeline automatically whenever new code is pushed to the repository.

By following these steps, I successfully set up the necessary infrastructure, configured each instance properly, and ensured that the CI/CD pipeline is ready to handle automated deployments triggered by GitHub webhooks.

## Challenges Faced

- **Cleaning Up Old Builds**:
  - Too many builds were eating up disk space with old Docker images, which could crash the server. I added a stage in the Jenkins pipeline to clean up these old images and builds.

- **Security Groups**:
  - Setting up security groups to keep the network communication secure and functional was tricky. I had to make sure the rules allowed the necessary traffic while keeping everything safe.

- **Handling Credentials**:
  - Keeping all the credentials (GitHub tokens, Docker creds, SSH keys) secure and correctly set up in Jenkins was a bit of a headache. I had to double-check everything to make sure it was all good.

- **Terraform Dependencies**:
  - Making sure Terraform created resources in the right order was a challenge. I had to keep validating the config to avoid messing things up.

- **Monitoring Setup**:
  - Getting Prometheus and Grafana up and running took some effort. I had to configure them to collect and show the right metrics and make sure Node Exporter was working on the Node.js instance.

- **Setting Up Webhooks**:
  - Setting up the GitHub webhook to automatically trigger Jenkins builds was a bit fiddly. I had to make sure it pointed to the right place and handled 


---
