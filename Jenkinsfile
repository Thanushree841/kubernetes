pipeline {
    agent any

    environment {
        IMAGE_NAME = "9148092892/myapp"
        DEPLOYMENT_FILE = "k8s/deployment.yaml"
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/Thanushree841/kubernetes.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker build -t $IMAGE_NAME:${BUILD_NUMBER} ."
                }
            }
        }

        stage('Login to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: '13826fff-e199-442b-b451-bbc79e53928e	', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh "echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin"
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    sh "docker push $IMAGE_NAME:${BUILD_NUMBER}"
                    // Tag as latest and push
                    sh "docker tag $IMAGE_NAME:${BUILD_NUMBER} $IMAGE_NAME:latest"
                    sh "docker push $IMAGE_NAME:latest"
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    // Update the image in deployment.yaml
                    sh "sed -i 's|image:.*|image: $IMAGE_NAME:${BUILD_NUMBER}|' ${DEPLOYMENT_FILE}"
                    // Apply YAML
                    sh "kubectl apply -f k8s/deployment.yaml"
                    sh "kubectl apply -f k8s/service.yaml"
                }
            }
        }
    }

    post {
        success {
            echo " Deployment succeeded!"
        }
        failure {
            echo " Deployment failed!"
        }
    }
}
