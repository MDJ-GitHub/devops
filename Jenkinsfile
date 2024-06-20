pipeline {
    agent any
	
	triggers {
        pollSCM('* * * * *')
    }
	
    stages {
	
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/MDJ-GitHub/devops.git'
            }
        }
		
        stage('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build("mdjdocker/devops")
                }
            }
        }
		
        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerCreds') {
                        dockerImage.push("latest")
                    }
                }
            }
        }
		
		stage('Deploy to Minikube') {
            steps {
                script {
                    sh 'kubectl apply -f deployment.yaml'
                }
            }
        }
    }
}