pipeline {
    agent any

    environment {
        AWS_REGION = 'us-west-2' // Set AWS region
        EKS_CLUSTER_NAME = 'kube-autoscale-cluster' // Set EKS cluster name
        DOCKER_IMAGE = 'prathyushamogili729/scrapyd-test:v3"' // Docker repository and image name
        K8S_NAMESPACE = 'web-scraper' // Kubernetes namespace
        KUBECONFIG_CREDENTIALS_ID = 'kubeconfig-credentials-id' // Need to Replace with Jenkins credentials ID
        DOCKER_CREDENTIALS_ID = 'docker-credentials-id' // Need to Replace with Jenkins credentials ID
    }

    stages {
        stage('Checkout') {
            steps {
                // Checkout the code from your repository
                git 'https://github.com/your-repo/web-scraper.git' // Replace with your repo URL
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Build the Docker image
                    sh 'docker build -t $DOCKER_IMAGE .'
                }
            }
        }

        stage('Run Tests') {
            steps {
                script {
                    // Run tests (adjust according to your testing framework)
                    sh 'pytest tests/'
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    // Log in to ECR (if using AWS ECR)
                    sh 'aws ecr get-login-password --region $AWS_REGION | docker login --username AWS --password-stdin your-ecr-repo' // Change to your ECR repo

                    // Push the Docker image
                    sh 'docker push $DOCKER_IMAGE'
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    // Set kubectl context to your EKS cluster
                    sh 'aws eks update-kubeconfig --region $AWS_REGION --name $EKS_CLUSTER_NAME'

                    // Apply Kubernetes manifests (adjust to your setup)
                    sh 'kubectl apply -f k8s/deployment.yaml' // Path to your Kubernetes deployment manifest
                    sh 'kubectl apply -f k8s/service.yaml' // Path to your Kubernetes service manifest
                    sh 'kubectl apply -f k8s/hpa.yaml' // Path to your Horizontal Pod Autoscaler manifest
                }
            }
        }

        stage('Post-Deployment Testing') {
            steps {
                script {
                    // Implement testing logic for deployed services, if necessary
                    // e.g., health checks or running integration tests
                    sh 'curl -f http://your-service-endpoint/health' // Replace with your service endpoint
                }
            }
        }
    }

    post {
        success {
            // Notify on success (you can customize this)
            echo 'Deployment Successful!'
        }
        failure {
            // Notify on failure
            echo 'Deployment Failed!'
        }
    }
}