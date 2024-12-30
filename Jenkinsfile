pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'samjsx/i-httpd'
        DOCKER_TAG = 'latest'
        KUBE_CONTEXT = 'minikube' // Kubernetes context for minikube
    }

    stages {
        stage('Build Docker Image') {
            steps {
                script {
                    // Build Docker image
                    sh 'docker build -t $DOCKER_IMAGE:$DOCKER_TAG .'
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    // Login to Docker Hub and push image
                    sh 'docker login -u $DOCKER_USERNAME -p $DOCKER_PASSWORD'
                    sh 'docker push $DOCKER_IMAGE:$DOCKER_TAG'
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    // Deploy using Helm
                    sh 'helm upgrade --install i-httpd ./backend-chart --set image.tag=$DOCKER_TAG'
                }
            }
        }

        stage('Test Deployment') {
            steps {
                script {
                    // Test if the service is running
                    sh "curl http://$(minikube ip):30519"
                }
            }
        }
    }

    post {
        always {
            // Clean up
            echo 'Cleaning up after build'
        }

        success {
            echo 'Build and deploy successful!'
        }

        failure {
            echo 'Build or deploy failed!'
        }
    }
}
