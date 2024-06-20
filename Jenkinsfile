pipeline {
    agent any
    stages {
        stage('Clone Repository') {
            steps {
                git 'https://github.com/MDJ-GitHub/devops.git'
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
                    docker.withRegistry('https://registry.hub.docker.com', 'MDJ.PassWoRd-09890762-rekcod/2021') {
                        dockerImage.push("latest")
                    }
                }
            }
        }
    }
}