pipeline {
    agent any

    environment {
        DOCKER_HUB_CREDENTIALS = 'dockerhub-credentials' // Jenkins credentials ID for DockerHub
    }

    triggers {
        pollSCM('* * * * *') // Poll GitHub every minute
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/mdj-github/devops.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', DOCKER_HUB_CREDENTIALS) {
                        def dockerImage = docker.build('mdjdocker/devops:latest', '.')
                        dockerImage.push()
                    }
                }
            }
        }

        stage('Deploy to Minikube') {
            steps {
                script {
                    // For Windows, use 'kubectl apply' directly without 'nohup'
                    bat 'kubectl apply -f deployment.yaml'
                }
            }
        }
    }
}