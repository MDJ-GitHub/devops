pipeline {
    agent any

    environment {
        DOCKER_HUB_CREDENTIALS = 'dockerCreds'
        KUBECONFIG = 'C:\\Users\\MDJMi\\.kube\\config'
		DOCKER_IMAGE = 'mdjdocker/devops:latest' // KUBECTL DOES NOT USE VAIRABLES, CHANGE IT MANUALLY THERE.
		GITHUB_REPO = 'https://github.com/MDJ-GitHub/devops.git' 
    }

    triggers {
        pollSCM('* * * * *')
    }

    stages {
        stage('1/3 Checkout') {
            steps {
                git branch: 'main', url: GITHUB_REPO
            }
        }

        stage('2/3 Build Docker Image') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', DOCKER_HUB_CREDENTIALS) {
                        def dockerImage = docker.build(DOCKER_IMAGE, '.')
                        dockerImage.push()
						docker.image(DOCKER_IMAGE).pull()
                    }
                }
            }
        }

        stage('3/3 Deploy to Minikube') {
            steps {
				script {
					def timestamp = System.currentTimeMillis()
                    env.TIMESTAMP = "${timestamp}" // Store the timestamp in an environment variable
                    bat 'kubectl config use-context minikube'
					bat 'kubectl apply -f Kubernetesfile.yaml'
                    bat "kubectl set image deployment/devops-deployment devops=%DOCKER_IMAGE% --record"
                    bat "kubectl patch deployment devops-deployment -p \"{\\\"spec\\\":{\\\"template\\\":{\\\"metadata\\\":{\\\"annotations\\\":{\\\"timestamp\\\":\\\"%TIMESTAMP%\\\"}}}}}\""
                    bat 'kubectl rollout status deployment/devops-deployment'
              
                }
            }
        }
    }
}