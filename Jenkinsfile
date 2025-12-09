/* groovylint-disable-next-line CompileStatic */
pipeline {
    aganet any
    tools {
        maven 'muMaven'
    }
    environment {
        IMAGE_NAME = 'gopikrishna1338/user-image'
        IMAGE_TAG = '2'
    }
    stages{
        stage('checkout'){
            steps{
                git 'https://github.com/gopiseshu/user-service.git'
            }
        }
        stage('Build Maven'){
            steps{
                sh 'mvn clean package -DskipTests'
            }
        }
        stage('Build Docker'){
            steps{
                sh 'docker built -t ${IMAGE_NAME}:${IMAGE_TAG} .'
            }
        }
        stage{'Push Image'}{
            steps{
                scripts{
                    sh """
                     docker.withRegistry('',''dockerid)
                     docker push ${IMAGE_NAME}:${IMAGE_TAG}
                     """
                }
            }
        }
        stage('Deploy Image'){
            steps{
                withKubeConfig(credentialsId: 'kubeconfig') {
                    bat """
                        kubectl set image deployment/${JOB_NAME}-deployment ${JOB_NAME}=${IMAGE_NAME}:${IMAGE_TAG} --record
                    """
                }
            }
        }

    }
}
