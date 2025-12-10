/* groovylint-disable-next-line CompileStatic */
pipeline {
    agent any

    tools {
        maven 'myMaven'
    }

    environment {
        IMAGE_NAME = 'gopikrishna1338/user-image'
        IMAGE_TAG  = '2'
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
            agent {
                docker {
                    image 'docker:24' 
                    args '--privileged -v /var/run/docker.sock:/var/run/docker.sock'
                }
            }
            steps {
                withEnv(["DOCKER_HOST=unix:///var/run/docker.sock"]) {
                    sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
                }
            }
        }

        stage('Push Image') {
            agent {
                docker {
                    image 'docker:24'
                    args '--privileged -v /var/run/docker.sock:/var/run/docker.sock'
                }
            }
            steps {
                withEnv(["DOCKER_HOST=unix:///var/run/docker.sock"]) {
                    script {
                        docker.withRegistry('', 'dockerid') {
                            sh "docker push ${IMAGE_NAME}:${IMAGE_TAG}"
                        }
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

    post {
        success {
            echo "Pipeline completed successfully!"
        }
        failure {
            echo "Pipeline failed!"
        }
    }
}
