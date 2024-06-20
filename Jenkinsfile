pipeline {
    agent any

    environment {
        DOCKER_HUB_CREDENTIALS = 'dockerCreds'
    }

    triggers {
        pollSCM('* * * * *')
    }

    stages {
        stage('1/3 Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/MDJ-GitHub/devops.git'
            }
        }

        stage('2/3 Build Docker Image') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', DOCKER_HUB_CREDENTIALS) {
                        def dockerImage = docker.build('mdjdocker/devops:latest', '.')
                        dockerImage.push()
                    }
                }
            }
        }

        stage('3/3 Deploy to Minikube') {
            steps {
                script {
                    bat 'minikube kubectl -- apply -f deployment.yaml'
                }
            }
        }
    }
}