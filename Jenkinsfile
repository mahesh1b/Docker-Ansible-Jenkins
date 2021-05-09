pipeline{
    agent any
    
    tools{
        maven 'maven3' 
    }
    
    environment {
      DOCKER_TAG = getVersion()
    }
    
    stages{
        stage('SCM'){
            steps{
                git 'https://github.com/mahesh1b/Docker-Ansible-Jenkins'   
            }
        }
        
        stage('Maven Build'){
            steps{
                sh 'mvn clean package'   
            }
        }
        
        stage('Docker Build'){
            steps{
                sh 'docker build . -t mahesh1b/myapp:${DOCKER_TAG} '   
            }
        }
        
        stage('Docker Push'){
            steps{
                withCredentials([string(credentialsId: 'docker-hub', variable: 'pass')]) {
                sh 'docker login -u mahesh1b -p ${pass}'
            }
                sh 'docker push mahesh1b/myapp:${DOCKER_TAG} '   
            }
        }
        
        stage('Docker Deploy'){
            steps{
              ansiblePlaybook credentialsId: 'dev', disableHostKeyChecking: true, extras: "-e DOCKER_TAG=${DOCKER_TAG}", installation: 'ansible', inventory: 'dev.inv', playbook: 'deploy-docker.yml'
            }
        }

    }
}


def getVersion(){
    def commitHash = sh label: '', returnStdout: true, script: 'git rev-parse --short HEAD'
    return commitHash
}
