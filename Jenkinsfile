pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'ton_username_dockerhub/fastapi-app'
        KUBE_NAMESPACE = 'default' // ou autre namespace selon ta config
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', credentialsId: 'GIT_CREDENTIALS_ID', url: 'https://github.com/alphaly94/Jenkins_devops_exams.git'
            }
        }

        stage('Build & Push Docker') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'DOCKER_CREDENTIALS_ID') {
                        def app = docker.build("${DOCKER_IMAGE}:${env.BUILD_ID}")
                        app.push()
                        app.push('latest')
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    sh "kubectl set image deployment/fastapi-app fastapi=${DOCKER_IMAGE}:${env.BUILD_ID} -n ${KUBE_NAMESPACE}"
                }
            }
        }
    }
}
