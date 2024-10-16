# README

## Overview

This document provides a brief overview of the approach, choices, and strategies for scaling and failover in the `scrapyd-k8s` project. The project leverages Kubernetes for orchestration and management of Scrapyd instances.

## Approach

The primary approach is to use Kubernetes to manage the deployment, scaling, and failover of Scrapyd instances. This involves creating various Kubernetes resources such as Deployments, Services, and ConfigMaps.

## Choices

1. **Kubernetes**: Chosen for its robust orchestration capabilities and ease of scaling.
2. **Scrapyd**: A popular choice for managing web scraping tasks.
3. **YAML Configuration**: All Kubernetes resources are defined using YAML files for clarity and version control.

## Strategies

### Scaling

- **Horizontal Pod Autoscaler (HPA)**: Utilized to automatically scale the number of Scrapyd instances based on CPU utilization or other custom metrics.
- **Deployment**: The `scrapyd-deployment.yaml` file defines the desired state for Scrapyd instances, including the number of replicas.

### Failover

- **ReplicaSets**: Ensures that a specified number of Scrapyd instances are always running. If an instance fails, a new one is automatically created.
- **Service**: The `scrapyd-service.yaml` file defines a stable endpoint for accessing Scrapyd instances, distributing traffic among available pods.

## YAML Files

- **scrapyd-deployment.yaml**: Defines the Deployment for Scrapyd instances.
- **scrapyd-service.yaml**: Defines the Service for accessing Scrapyd instances.
- **hpa.yaml**: Configures the Horizontal Pod Autoscaler for Scrapyd.

## Conclusion

By leveraging Kubernetes, this project ensures efficient scaling and robust failover mechanisms for Scrapyd instances, providing a reliable and scalable web scraping infrastructure.

## CI/CD Flow

The CI/CD pipeline for the `scrapyd-k8s` project is managed using Jenkins. The Jenkinsfile defines the stages and steps required to build, test, and deploy the application.

### Jenkinsfile Overview

The Jenkinsfile consists of the following stages:

1. **Checkout**: Retrieves the latest code from the source repository.
2. **Build Docker Image**: Compiles the application and builds Docker images for Scrapyd.
3. **Push Docker Image**: Pushes the Docker images to a container registry.
4. **Deploy to Kubernetes**: Deploys the application to the Kubernetes cluster using `kubectl`.
5. **Run Tests**: Executes commands to verify the deployment.
6. **Automated Testing**: Runs unit tests and integration tests to ensure the application is functioning correctly.

### Stages

#### Checkout

- **Checkout SCM**: Retrieves the latest code from the source repository.

#### Build Docker Image

- **Docker Build**: Uses a Dockerfile to build the Scrapyd image.

#### Push Docker Image

- **Docker Push**: Pushes the tagged Docker image to a container registry such as Docker Hub or Google Container Registry.

#### Deploy to Kubernetes

- **Kubectl Apply**: Uses `kubectl` to apply the Kubernetes YAML files (`kubernetes.yaml`, `metrics-server.yaml`, `hpa.yaml`, `pdb.yaml`) to the cluster.

#### Run Tests

- **Kubectl Commands**: Runs `kubectl` commands to verify the deployment.

#### Automated Testing

- **Pytest**: Runs unit tests using the `pytest` framework.

This Jenkinsfile provides a streamlined CI/CD process, ensuring that the `scrapyd-k8s` project is built, tested, and deployed efficiently.