pipeline {
    agent any 
	tools {
		maven 'maven-home'
		}
	environment {
		DOCKER_TAG = "getVersion"
	}
    stages {
        stage('SCM Checkout') { 
            steps {
                git credentialsId: 'git-credentials', 
											url: 'https://github.com/shankar244/dockeransiblejenkins.git'
            }
        }
        stage('Maven Clean Package') { 
            steps {
               sh " mvn clean package"
            }
        }
		stage('Custom Docker Image') { 
            steps {
                sh " docker build . -t shankar244/mycustom-image:${DOCKER_TAG} " 
			}
		}
		stage('Docker Image Push') { 
            steps {
			withCredentials([string(credentialsId: 'dockerhubpwd', variable: 'dockerhub')]) {
				sh "docker login -u shankar244 -p ${dockerhub}"
				}	
                sh " docker push shankar244/mycustom-image:${DOCKER_TAG} " 
            }
		}
		stage('Ansible Deployment') { 
            steps {
				ansiblePlaybook credentialsId: 'ansible-dev-server1', disableHostKeyChecking: true, extras: "-e DOCKER_TAG=${DOCKER_TAG}", inventory: 'dev.inv', playbook: 'deploy-docker.yml'
            }
        }
	}
}
def getVersion () {
	def commitHash = sh label: '', returnStdout: true, script: 'git rev-parse --short HEAD'
	return commitHash
}
