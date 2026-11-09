pipeline{
    agent any
    environment{
        IMAGE_NAME = 'noasteinberg/flask-app'
    }
    stages{
        stage("Checkout Code"){
            steps{
                git branch: 'main', url: 'https://github.com/noa-steinberg/flask-k8s-cicd.git'
            }
        }
        stage("Docker Build+Tag"){
            steps{
                sh 'docker build -t ${IMAGE_NAME}:${BUILD_NUMBER} -t ${IMAGE_NAME}:latest .'
            }
        }
        stage("Docker Push"){
            steps{
                withCredentials([usernamePassword(credentialsId:'dockerhub-credentials', usernameVariable:'DOCKER_USER', passwordVariable:'DOCKER_PASS')]){
                    sh '''
                        echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                        docker push ${IMAGE_NAME}:${BUILD_NUMBER}
                        docker push ${IMAGE_NAME}:latest
                    '''
                }
            }
        }
        stage("Deployment"){
            steps{
                sh 'kubectl apply -f deployment.yaml'
            }
        }
    }
}