pipeline {
    agent any

    environment {
        AWS_REGION = 'us-west-2' // Set AWS region
        EKS_CLUSTER_NAME = 'kube-autoscale-cluster' // Set EKS cluster name
        DOCKER_IMAGE = 'prathyushamogili729/scrapyd-test:v3' // Docker repository and image name
        K8S_NAMESPACE = 'web-scraper' // Kubernetes namespace
        KUBECONFIG_CREDENTIALS_ID = 'kubeconfig-credentials-id' // Replace with Jenkins credentials ID
        DOCKER_CREDENTIALS_ID = 'docker-credentials-id' // Replace with Jenkins credentials ID
    }

    stages {
        stage('Checkout') {
            steps {
                // Checkout the code from your repository
                git 'https://github.com/connect2prathyushagithub/scrapyd-k8s'
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

        stage('Run Unit Tests') {
            steps {
                script {
                    // Run unit tests
                    sh 'pytest tests/unit/'
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    // Log in to ECR (if using AWS ECR)
                    sh 'aws ecr get-login-password --region $AWS_REGION | docker login --username AWS --password-stdin your-ecr-repo' // Replace with your ECR repo

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

                    // Deploy Kubernetes deployment
                    sh 'kubectl apply -f k8s/deployment.yaml' 
                    sh 'kubectl apply -f k8s/metrics-server.yaml'
                    sh 'kubectl apply -f k8s/pdb.yaml'
                    sh 'kubectl apply -f k8s/hpa.yaml' 
                }
            }
        }

        stage('Post-Deployment Testing') {
            steps {
                script {
                    // Check the status of the deployment
                    sh 'kubectl rollout status deployment/scrapyd-k8s -n $K8S_NAMESPACE'
                    
                    // Verify the service is running
                    sh 'kubectl get svc -n $K8S_NAMESPACE'
                }
            }
        }

        stage('Run Integration Tests') {
            steps {
                script {
                    // Run integration tests to validate the scrapers are functioning as expected
                    sh 'pytest tests/integration/'
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment Successful!'
        }
        failure {
            echo 'Deployment Failed!'
        }
    }
}