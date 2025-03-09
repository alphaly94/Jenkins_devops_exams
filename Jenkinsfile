pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "docker.io/alphaly94/fastapi-examen"  // Remplace par ton DockerHub
        KUBE_NAMESPACE = "dev"  // Namespace de déploiement Kubernetes
    }

    stages {
        stage('Checkout') {
            steps {
                git credentialsId: 'GIT_CREDENTIALS_ID', url: 'https://github.com/alphaly94/Jenkins_devops_exams.git'
            }
        }

        stage('Build & Push Docker') {
            steps {
                script {
                    withDockerRegistry([credentialsId: 'DOCKERHUB_CREDENTIALS_ID', url: '']) {
                        sh """
                        docker build -t ${DOCKER_IMAGE}:${env.BUILD_NUMBER} .
                        docker push ${DOCKER_IMAGE}:${env.BUILD_NUMBER}
                        """
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            when {
                branch 'main'
            }
            steps {
                withKubeConfig([credentialsId: 'KUBECONFIG_CREDENTIALS_ID']) {
                    sh """
                    kubectl apply -f k8s/
                    helm upgrade --install fastapi-app ./fastapi-chart --namespace=${KUBE_NAMESPACE}
                    """
                }
            }
        }
    }
}
