# Wordsmith App with CI/CD and Deployment via Ansible

## Overview
This project is based on the open-source repository [Wordsmith App](https://github.com/dockersamples/wordsmith/tree/main). It includes a database, an API, and a web application. Enhancements to the deployment process and CI/CD pipeline have been made using Ansible and GitHub Actions.

## Features
- **CI/CD Pipelines**: Two GitHub workflows automate the build and deployment processes for the API and web components.
- **Containerization**: Docker images are created and pushed to Docker Hub.
- **Minikube Cluster Deployment**: The application is deployed on a Minikube cluster hosted on an AWS EC2 instance.
- **Infrastructure Automation**: Ansible playbooks automate EC2 instance provisioning and cluster setup.

## Prerequisites
1. An AWS account with permissions to create EC2 instances.
2. Ansible installed locally.
3. Docker and a Docker Hub account.
4. A GitHub repository containing the Wordsmith App source code and CI/CD workflows.

## Directory Structure
```
wordsmith_app/
|-- .github/
|   |-- workflows/
|   |   |-- call-docker-build-api.yml
|   |   |-- call-docker-build-web.yml
|-- deploy/
|   |-- playbook.yml
|-- api/
|   |-- Dockerfile
|-- web/
|   |-- Dockerfile
|-- db/
|   |-- words.sql
|-- k8s-manifests/
|   |-- api.yaml
|   |-- db.yaml
|   |-- web.yaml
|-- kustomization.yaml
|-- README.md
```

## Deployment Steps

### 1. Clone the Repository
```bash
git clone https://github.com/ShirGanon/wordsmith_app
cd wordsmith_app
```

### 2. Configure AWS Credentials
Ensure your AWS credentials are available locally (e.g., in `~/.aws/credentials`). These credentials will be used by Ansible to provision the EC2 instance. For temporary access, you can use `aws_access_key` and `aws_secret_key` (not recommended).

### 3. Run the Ansible Playbook
The provided Ansible playbook performs the following tasks:
1. Provisions an EC2 instance with a security group.
2. Installs Minikube, `kubectl`, and necessary dependencies.
3. Sets up a Kubernetes cluster on the EC2 instance.

Run the playbook:
```bash
cd deploy
ansible-playbook -i inventory playbook.yml
```
Modify the `inventory` file to include your EC2 details after provisioning.

### 4. CI/CD Workflows

#### 4.1 Build and Push Docker Images
The `call-docker-build-api.yml` and `call-docker-build-web.yml` workflows:
1. Build Docker images for the API and web components.
2. Push the images to Docker Hub.
3. Update the Minikube cluster with the latest images.

These workflows are triggered on each push to the repository. Ensure the following secrets are set in your GitHub repository:
- `DOCKERHUB_TOKEN`
- `DOCKERHUB_USERNAME`
- `EC2_HOST`
- `EC2_SSH_PRIVATE_KEY`

#### 4.2 Trigger Workflows
Push changes to your GitHub repository to automatically trigger the workflows:
```bash
git add .
git commit -m "Update application"
git push origin main
```

### 5. Verify Deployment
1. Ensure all pods, services, and deployments are running successfully:
   ```bash
   kubectl get all
   ```
2. Access the application using the Minikube service URL:
   ```bash
   minikube service web
   ```

## Additional Notes
- **Monitoring**: Consider implementing monitoring tools like Prometheus and Grafana for real-time metrics.
- **Scaling**: For production environments, consider using a managed Kubernetes service like Amazon EKS or Google GKE.

## References
- [Wordsmith App](https://github.com/dockersamples/wordsmith)
- [DockerHub Repo](https://hub.docker.com/u/shirg3)

