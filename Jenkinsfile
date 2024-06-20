pipeline {
    agent any

    environment {
        DOCKER_HUB_CREDENTIALS = 'dockerCreds'
        KUBECONFIG = 'C:\\Users\\MDJMi\\.kube\\config'
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
                    bat '''
                        kubectl config use-context minikube
                        kubectl apply -f deployment.yaml --as=system:serviceaccount:default:jenkins-sa
                    '''
                }
            }
        }
    }
}