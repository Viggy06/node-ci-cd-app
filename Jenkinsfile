pipeline {
    agent any

    environment {
        IMAGE_NAME = "captianvigg06/node-ci-cd"
        IMAGE_TAG = "latest"
    }

    parameters {
        string(name: 'EC2_HOST', defaultValue: 'ubuntu@13.233.38.146', description: 'EC2 SSH target (user@host)')
        credentials(name: 'EC2_CREDENTIALS', defaultValue: 'ec2-ssh-key', description: 'EC2 SSH key credential')
    }

    stages {
        
        stage('Checkout Code') {
            steps {
                echo "Cloning repository..."
                checkout scm
            }
        }

        stage('Connect to EC2') {
            steps {
                script {
                    echo "Connecting to EC2: ${params.EC2_HOST}"

                    sshagent([params.EC2_CREDENTIALS]) {
                        sh """
                            ssh -o StrictHostKeyChecking=no ${params.EC2_HOST} '
                                echo "✅ Connected successfully to EC2"
                                whoami
                                pwd
                                ls
                            '
                        """
                    }
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker --version"
                    echo "Building Docker image..."
                    sh "ls"
                    sh "pwd"
                    sh "whoami"
                    sh "docker ps -a"
                    sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
                }
            }
        }

        stage('Login to Docker Hub') {
            steps {
                script {
                    echo "Logging in to Docker Hub..."
                    withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                        sh "echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin"
                    }
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    echo "Pushing image to Docker Hub..."
                    sh "docker push ${IMAGE_NAME}:${IMAGE_TAG}"
                }
            }
        }

        stage('Deploy Container') {
            steps {
                script {
                    echo "Deploying container..."
                    // stop and remove any existing container
                    sh "docker rm -f node-app || true"
                    // run the new container
                    sh "docker run -d -p 3000:3000 --name node-app ${IMAGE_NAME}:${IMAGE_TAG}"
                }
            }
        }
    }

    post {
        always {
            echo "Cleaning up..."
            sh "docker system prune -f"
        }
        success {
            echo "✅ Deployment successful!"
        }
        failure {
            echo "❌ Build failed!"
        }
    }
}
