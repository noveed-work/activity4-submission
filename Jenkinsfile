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
                withCredentials([usernamePassword(credentialsId: 'docker-hub', passwordVariable: 'docker-hubPassword', usernameVariable: 'docker-hubUser')]){
                    sh 'docker login -u ${env.docker-hubUser} -p ${env.docker-hubPassword}'
                    sh 'docker push noveedwork/activity4:app'
                }
            }
        }
    }
}