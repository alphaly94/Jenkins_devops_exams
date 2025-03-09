pipeline {
    agent {
        kubernetes {
            yaml """
apiVersion: v1
kind: Pod
metadata:
  labels:
    jenkins-agent: "true"
spec:
  containers:
  - name: docker
    image: docker:latest
    command:
    - cat
    tty: true
    volumeMounts:
    - mountPath: "/var/run/docker.sock"
      name: docker-socket
  volumes:
  - name: docker-socket
    hostPath:
      path: /var/run/docker.sock
"""
        }
    }
    stages {
        stage('Build & Push Docker') {
            steps {
                script {
                    withCredentials([string(credentialsId: 'DOCKER_PASS', variable: 'DOCKER_PASSWORD')]) {
                        sh """
                        docker login -u alfonsodyka --password-stdin <<< "$DOCKER_PASSWORD"
                        docker build -t alfonsodyka/myimage:${env.BUILD_NUMBER} .
                        docker push alfonsodyka/myimage:${env.BUILD_NUMBER}
                        """
                    }
                }
            }
        }
    }
}
