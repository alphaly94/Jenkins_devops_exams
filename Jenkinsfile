pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "docker.io/alphaly94/fastapi-examen"  // Remplace par ton repo DockerHub
        KUBE_NAMESPACE = "dev"  // Namespace de déploiement Kubernetes
        DOCKER_CREDENTIALS = 'DOCKERHUB_CREDENTIALS_ID'
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
                    withCredentials([usernamePassword(credentialsId: DOCKER_CREDENTIALS, usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                        sh """
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
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
