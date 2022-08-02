pipeline{
    agent none
    environment{
        BUILD_SERVER='ec2-user@13.233.143.22'
        DEPLOY_SERVER='ec2-user@35.154.249.199'
        IMAGE_NAME='devopstrainer/java-mvn-privaterepos:php$BUILD_NUMBER'
    }
    stages{
        stage("Build the dockerimage and push to dockerhub"){
            agent any
            steps{
                script{
                    sshagent(['jenkins-slave']) {
                        withCredentials([usernamePassword(credentialsId: 'ssh-user', passwordVariable: 'PASSWORD', usernameVariable: 'USERNAME')]) {
                         echo "Packing the code and create a docker image"
                        sh "scp -o StrictHostKeyChecking=no -r docker-files ${BUILD_SERVER}:/home/ec2-user"
                sh "ssh -o StrictHostKeyChecking=no ${BUILD_SERVER} 'bash ~/docker-files/docker-script.sh'"
                sh "ssh ${BUILD_SERVER} sudo docker build -t ${IMAGE_NAME} /home/ec2-user/docker-files/"
                sh "ssh ${BUILD_SERVER} sudo docker login -u $USERNAME -p $PASSWORD"
                sh "ssh ${BUILD_SERVER} sudo docker push ${IMAGE_NAME}"
                  }
                    }
                }
            }

        }
        stage("Run the dockerimage using docker-compose"){
                  agent any
                  steps{
                    script{
                        sshagent(['jenkins-slave']) {
                         withCredentials([usernamePassword(credentialsId: 'ssh-user', passwordVariable: 'PASSWORD', usernameVariable: 'USERNAME')]) {
                          sh "scp -o StrictHostKeyChecking=no -r docker-files ${DEPLOY_SERVER}:/home/ec2-user"
                          sh "ssh -o StrictHostKeyChecking=no ${DEPLOY_SERVER} 'bash ~/docker-files/docker-script.sh'"
                         sh "ssh ${DEPLOy_SERVER} sudo docker login -u $USERNAME -p $PASSWORD"
                         sh "ssh -o StrictHostKeyChecking=no ${DEPLOY_SERVER} 'bash ~/docker-files/docker-compose-script.sh ${IMAGE_NAME}'"
                         }
                        }
                    }
                  }
        }
    }
}