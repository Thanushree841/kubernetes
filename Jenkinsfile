pipeline {
    agent { label 'docker' }

    environment {
        DOCKER_REPO_CREDENTIALS = 'a5528c83-e532-4484-ae9b-135493e3957b'
        DOCKER_IMAGE = '9148092892/kubernetes'          
        GIT_REPO = 'https://github.com/Thanushree841/kubernetes.git'
        BRANCH = 'main'
    }

    stages {
        stage('Clone Repository') {
            steps {
                echo "Cloning repository from ${GIT_REPO}"
                git branch: "${BRANCH}", url: "${GIT_REPO}"
            }
        }

       stage('Build Docker Image') {
            steps {
                script {
            
                    IMAGE_TAG_LATEST = "${DOCKER_IMAGE}:latest"

                    echo "Building Docker image with tags:${IMAGE_TAG_LATEST}"
                    sh "docker build -t ${IMAGE_TAG_LATEST} ."
                }
            }
        }

        stage('Authenticate with Docker Registry') {
            steps {
                script {
                    echo "Logging in to Docker registry"
                    withCredentials([usernamePassword(credentialsId: "${DOCKER_REPO_CREDENTIALS}", usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                        sh "echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin"
                    }
                }
            }
        

        stage('Push Docker Images') {
            steps {
                script {
                    echo "Pushing images to registry"
                    echo "IMAGE_TAG_LATEST: ${IMAGE_TAG_LATEST}
                
                    sh "docker push ${IMAGE_TAG_LATEST}"
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
}

