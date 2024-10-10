pipeline {
    agent any

    environment {
        // Set the KUBECONFIG for the Jenkins user
        KUBECONFIG = "$HOME/.kube/config"
    }

    stages {
        stage("Cleanup") {
            steps {
                // Cleanup any running or stopped containers, and remove all images
                sh 'docker rm -f $(docker ps -aq) || true'
                sh 'docker rmi -f $(docker images -q) || true'
            }
        }

        stage("Start Minikube") {
            steps {
                script {
                    // Ensure Minikube is running
                    def minikubeRunning = sh(script: 'minikube status | grep -q "Running"', returnStatus: true)
                    if (minikubeRunning != 0) {
                        // Start Minikube if not already running
                        sh 'minikube start --cpus 4 --memory 8192 --driver=docker'
                    }
                    // Set kubectl context to minikube
                    sh 'kubectl config use-context minikube'
                }
            }
        }

        stage("Build Docker Image") {
            steps {
                // Build the Docker image
                sh 'docker build -t noveedwork/activity4:app .'
            }
        }

        stage("Push Image to Docker Hub") {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-hub', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                    // Login and push the Docker image to Docker Hub
                    sh '''
                    echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin
                    docker push docker.io/noveedwork/activity4:app
                    '''
                }
            }
        }

        stage("Orchestrate Containers on Kubernetes") {
            steps {
                // Apply the Kubernetes deployment and service YAML files
                sh 'kubectl apply -f flask_deployment.yaml'
                sh 'kubectl apply -f flask_service.yaml'
                sh 'kubectl apply -f nginx_deployment.yaml'
                sh 'kubectl apply -f nginx_service.yaml'
            }
        }
    }

    post {
        always {
            // Optionally, print the Minikube status at the end of the pipeline
            sh 'minikube status'
        }
    }
}
