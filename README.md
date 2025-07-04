# README.md

## Project Overview

This project demonstrates a complete CI/CD pipeline for a Python-based web application deployed on AWS EKS using Docker, Helm, and GitHub Actions.

## Prerequisites

Before setting up and running the project, ensure the following tools and accounts are configured:

* AWS CLI (configured with appropriate IAM credentials)
* Terraform v1.6+
* kubectl
* Docker
* Helm
* GitHub account with repository created
* Docker Hub account

## Project Structure

```
project-root/
├── app_files/                  # Python source code
│   ├── app.py
│   └── requirements.txt
├── Dockerfile                  # Multi-stage Dockerfile
├── HelmCharts/                 # Helm chart directory
│   ├── templates/
│   │   ├── deployment.yaml
│   │   ├── service.yaml
│   │   ├── hpa.yaml
│   │   ├── configmap.yaml
│   │   └── secret.yaml
│   └── values.yaml             # Helm values file (minimal if used)
├── .github/workflows/
│   └── ci-cd.yaml              # GitHub Actions workflow file
├── EKS_IAC/                    # Terraform files to create EKS
│   ├── main.tf
│   ├── variables.tf
│   ├── outputs.tf
│   └── providers.tf
└── README.md                   # This file
```

## 1. Infrastructure Setup

### Step 1: Initialize Terraform

```bash
cd EKS_IAC
terraform init
```

### Step 2: Apply Terraform

```bash
terraform apply -auto-approve
```

This will provision:

* EKS cluster
* IAM roles
* VPC/Subnets
* Node group with t3.small instances (max 2)

### Step 3: Update kubeconfig

```bash
aws eks update-kubeconfig --region <region> --name <cluster_name>
```

## 2. Docker Image Build and Push

### Step 1: Build Image

```bash
docker build -t <dockerhub-username>/app:v1 .
```

### Step 2: Push Image

```bash
docker push <dockerhub-username>/app:v1
```

## 3. Deploy with Helm

### Step 1: Set image tag and repo (if not using values.yaml)

```bash
helm upgrade --install app ./HelmCharts \
  --set image.repository=<dockerhub-username>/app \
  --set image.tag=v1
```

### Step 2: Verify Deployment

```bash
kubectl get all
```

### Step 3: Port-forward to test locally

```bash
kubectl port-forward svc/app 8080:80
```

Visit: [http://localhost:8080](http://localhost:8080)

## 4. CI/CD with GitHub Actions

### GitHub Secrets to Configure

Add these secrets in your GitHub repository:

* `AWS_ACCESS_KEY_ID`
* `AWS_SECRET_ACCESS_KEY`
* `AWS_REGION`
* `CLUSTER_NAME`
* `DOCKER_USERNAME`
* `DOCKER_PASSWORD`

### CI/CD Pipeline

The `.github/workflows/ci-cd.yaml` pipeline performs:

1. Trigger on push to main
2. Linting and unit tests
3. Docker build and push to Docker Hub
4. Helm upgrade to deploy on EKS

## 5. Observability (Optional)

To enable metrics and logging:

* Enable **CloudWatch Container Insights** in EKS
* Set up **CloudWatch Agent** or `aws-for-fluent-bit` DaemonSet for logs

---

## Cleanup

To delete all AWS resources:

```bash
cd EKS_IAC
terraform destroy -auto-approve
```

---

## Author

Suyash Kesharwani

the folder name is EKS\_IAC
