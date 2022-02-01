pipeline {
    agent any 
	tools {
		maven 'maven home'
		}
    environment {
      DOCKER_TAG = getVersion()
    }
    stages {
        stage('SCM Checkout') { 
            steps {
                git credentialsId: 'git-credential',
         				url: 'https://github.com/vikas99341/dockeransiblejenkins.git' 
            }
        }
        stage('Maven Clean') { 
            steps {
                sh " mvn clean package"
            }
        }
        stage('Custom Docker Image') { 
            steps {
                sh " docker build . -t vikas24775/evening-image:${DOCKER_TAG} " 
            }
        }
        stage('Docker Image Push') { 
            steps {
			withCredentials([string(credentialsId: 'docker-hub', variable: 'dockerHubPwd')]) {
				sh "docker login -u vikas24775 -p ${dockerHubPwd}"
			}
                sh " docker push vikas24775/evening-image:${DOCKER_TAG} " 
            }
        }
        stage('Ansible Deployment') { 
            steps {
			    ansiblePlaybook credentialsId: 'ansible-dev-server', disableHostKeyChecking: true, extras: "-e DOCKER_TAG=${DOCKER_TAG}", installation: 'ansible_home', inventory: 'dev.inv', playbook: 'deploy-docker.yml'
            }
        }
    }
}
def getVersion(){
    def commitHash = sh label: '', returnStdout: true, script: 'git rev-parse --short HEAD'
    return commitHash
}
