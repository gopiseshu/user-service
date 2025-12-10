pipeline {
    agent any
    tools{
      maven 'myMaven'
    }

    environment {
        EC2_HOST = '13.126.106.239'
        EC2_USER = "ec2-user"
        IMAGE = 'gopikrishna1338/user-service:latest'
    }

    stages {

        stage('Checkout') {
            steps {
                git 'https://github.com/gopiseshu/user-service.git'
            }
        }

        stage('Build Maven') {
            steps {
                sh "mvn clean package -DskipTests"
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${IMAGE} ."
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('', 'dockerid') {
                        sh "docker push ${IMAGE}"
                    }
                }
            }
        }

        stage('Deploy to EC2') {
            steps {
                sshagent(['ec2-ssh']) {
                    sh """
                    ssh -o StrictHostKeyChecking=no ${EC2_USER}@${EC2_HOST} '
                        docker pull ${IMAGE} &&
                        docker stop user-service || true &&
                        docker rm user-service || true &&
                        docker run -d --name user-service -p 8080:8080 ${IMAGE}
                    '
                    """
                }
            }
        }

    }
}
