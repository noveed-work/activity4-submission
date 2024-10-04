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
                withCredentials([usernamePassword(credentialsId: 'docker-hub', passwordVariable: 'docker-hubPassword', usernameVariable: 'docker-hubUsername')]){
                    sh '''
                        #!/bin/bash
                        docker login -u $docker-hubUsername -p $docker-hubPassword
                        docker push docker.io/noveedwork/activity4:app
                    '''
                }
            }
        }
    }
}