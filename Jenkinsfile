pipeline{
    agent any
    stages{
        stage("Cleanup"){
            steps{
                sh 'docker rm -f \$(docker ps -aq) || true'
                sh 'docker rmi -f \$(docker images) || true'
                sh 'docker network create new-network || true'
            }
        }
        stage("Build image"){
            steps{
                sh 'docker build -t noveedwork/activity4:app .'
            }
        }
        stage("Pushing image to docker hub"){
            steps{
                withCredentials([usernamePassword(credentialsId: 'docker-hub', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                sh '''
                echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin
                docker push docker.io/noveedwork/activity4:app
                '''
                }
            }
        stage("Orchestrating container on Kubernetes"){
            steps{
                sh 'kubectl apply -f flask_deployment.yaml'
                sh 'kubectl apply -f flask_service.yaml'
                sh 'kubectl apply -f nginx_deployment.yaml'
                sh 'kubectl apply -f nginx_service.yaml'
            }
        }
        }
    }
}