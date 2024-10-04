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
                withCredentials([usernamePassword(credentialsID: 'dockerhub', passwordVariable: 'dockerhubPassword', usernameVariable: 'dockerhubUser')]){
                    sh 'docker login -u ${env.dockerhubUser} -p ${env.dockerhubPassword}'
                    sh 'docker push noveedwork/activity4:app'
                }
            }
        }
    }
}