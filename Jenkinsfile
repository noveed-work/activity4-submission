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
                withCredentials([usernamePassword(credentialsID: 'dockerhub', passwordVariable: 'dockerHubPassword', usernameVariable: 'dockerHubUser')]){
                    sh 'docker login -u ${env.dockerHubUser} -p ${env.dockerHubPassword}'
                    sh 'docker push noveedwork/activity4:app'
                }
            }
        }
    }
}