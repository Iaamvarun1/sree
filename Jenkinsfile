pipeline {
    agent any
    environment{
        DOCKER_TAG = getDockerTag()
    }
    stages{
        stage('Build Docker Image'){
            steps{
                sh "docker build . -t thotasreenu9/nodeapp:${DOCKER_TAG} "
            }
        }
        stage('DockerHub Push'){
            steps{
                   withCredentials([string(credentialsId: 'dockerHubPwd', variable: '')]) {
    
                    sh "docker login -u thotasreenu9 -p ${dockerHubPwd}"
                    sh "docker push thotasreenu9/nodeapp:${DOCKER_TAG}"
                }
            }
        }
        stage('Deploy to k8s'){
            steps{
                sh "chmod +X changeTag.sh"
				sh "./changeTag.sh ${DOCKER_TAG}"
				sshagent(['k8s-master']) {
				      sh "scp -o StrictHostKeyChecking=no services.yml node-app-pod.ymlec2-user@172.31.0.153:/home/ec2-user/"
            
				
			     script{
			     try{
				  sh "ssh ec2-user@172.31.0.153 kubectl apply -f ."
				 }catch(error){
				  sh "ssh ec2-user@172.31.0.153 kubectl create -f ."
				      }
			        }
				}
            }
        }
    }
}

def getDockerTag(){
    def tag  = sh script: 'git rev-parse HEAD', returnStdout: true
    return tag
 }
