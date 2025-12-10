pipeline {
    agent any

    tools {
        maven 'myMaven'
    }

    environment {
        IMAGE_NAME = 'gopikrishna1338/user-image'
        IMAGE_TAG  = '2'
        DOCKER_HOST = "unix:///var/run/docker.sock"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Maven') {
            steps {
                sh "mvn clean package -DskipTests"
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                    docker version
                    docker build -t ${IMAGE_NAME}:${IMAGE_TAG} .
                '''
            }
        }

        stage('Push Image') {
            steps {
                script {
                    docker.withRegistry('', 'dockerid') {
                        sh "docker push ${IMAGE_NAME}:${IMAGE_TAG}"
                    }
                }
            }
        }

        stage('Deploy Image to Kubernetes') {
            steps {
                withKubeConfig(credentialsId: 'kubeconfig') {
                    sh """
                        kubectl set image deployment/user-service-deployment \
                        user-service=${IMAGE_NAME}:${IMAGE_TAG} --record
                    """
                }
            }
        }
    }
}
