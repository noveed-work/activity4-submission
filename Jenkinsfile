pipeline {
    agent any
    environment {
        AZURE_CLIENT_ID = credentials('azure-client-id') // Reference the stored Client ID
        AZURE_CLIENT_SECRET = credentials('azure-client-secret') // Reference the stored Client Secret
        AZURE_TENANT_ID = credentials('azure-tenant-id') // Reference the stored Tenant ID
        AZURE_RESOURCE_GROUP = '<YourResourceGroupName>' // Resource group where AKS is deployed
        AZURE_AKS_CLUSTER = '<YourAKSClusterName>' // AKS cluster name
        AZURE_SUBSCRIPTION_ID = '<YourSubscriptionID>' // Azure subscription ID
    }

    stages {
        stage("Azure Login") {
            steps {
                // Log in to Azure using the Service Principal credentials
                script {
                    sh '''
                    az login --service-principal \
                        -u $AZURE_CLIENT_ID \
                        -p $AZURE_CLIENT_SECRET \
                        --tenant $AZURE_TENANT_ID

                    # Set the default subscription
                    az account set --subscription $AZURE_SUBSCRIPTION_ID
                    '''
                }
            }
        }

        stage("Configure AKS Access") {
            steps {
                // Configure kubectl to use the AKS cluster
                script {
                    sh '''
                    az aks get-credentials --resource-group $AZURE_RESOURCE_GROUP \
                        --name $AZURE_AKS_CLUSTER --overwrite-existing
                    '''
                }
            }
        }

        stage("Cleanup") {
            steps {
                // Cleanup any running or stopped containers, and remove all images
                sh '''
                docker rm -f $(docker ps -aq) || true
                docker rmi -f $(docker images -q) || true
                '''
            }
        }

        stage("Build Docker Image") {
            steps {
                // Build the Docker image from the Dockerfile in the current directory
                sh 'docker build -t noveedwork/activity4:app .'
            }
        }

        stage("Push Image to Docker Hub") {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-hub', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                    // Login to Docker Hub and push the image
                    sh '''
                    echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin
                    docker push noveedwork/activity4:app
                    '''
                }
            }
        }

        stage("Deploy to AKS") {
            steps {
                // Apply the Kubernetes deployment and service YAML files to AKS
                sh '''
                kubectl apply -f flask_deployment.yaml
                kubectl apply -f flask_service.yaml
                kubectl apply -f nginx_deployment.yaml
                kubectl apply -f nginx_service.yaml
                '''
            }
        }
    }

    post {
        always {
            // Print the Kubernetes cluster information after the deployment
            sh 'kubectl cluster-info'
        }
    }
}
