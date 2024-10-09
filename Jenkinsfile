pipeline {
    agent any

    environment {
        export KUBECONFIG=~/.kube/config // Specify Jenkins user's kube config
    }

    stages {
        stage("Cleanup") {
            steps {
                sh 'docker rm -f $(docker ps -aq) || true'
                sh 'docker rmi -f $(docker images) || true'
            }
        }
        stage("Start Minikube") {
            steps {
                // Ensure Minikube is running
                sh '''
                if ! minikube status | grep -q "Running"; then
                    minikube start --cpus 4 --memory 8192 --driver=docker
                fi
                '''
                // Set the context for kubectl
                sh 'kubectl config use-context minikube'
            }
        }
        stage("Build Image") {
            steps {
                sh 'docker build -t noveedwork/activity4:app .'
            }
        }
        stage("Push Image to Docker Hub") {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-hub', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                    sh '''
                    echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin
                    docker push docker.io/noveedwork/activity4:app
                    '''
                }
            }
        }
        stage("Orchestrate Containers on Kubernetes") {
            steps {
                // Apply Kubernetes YAML files
                sh 'kubectl apply -f flask_deployment.yaml'
                sh 'kubectl apply -f flask_service.yaml'
                sh 'kubectl apply -f nginx_deployment.yaml'
                sh 'kubectl apply -f nginx_service.yaml'
            }
        }
    }
}
