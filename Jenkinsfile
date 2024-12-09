pipeline {
    agent any

    environment {
        registry = "touatifadwa/demo-jenkins" // Replace with your Docker Hub username and repository
        registryCredential = 'docker-hub-credentials' // Match this with the credential ID created in Jenkins
        dockerImage = ''        
        CONTAINER_NAME = 'demo-jenkins'
    }

    triggers {
        pollSCM('*/5 * * * *') // Polls Git every 5 minutes for changes
    }

    stages {
        stage('Clone Repo') {
            steps {
                echo "Cloning repository..."
                git branch: 'main', url: 'https://github.com/wahid007/Jenkins-Demo.git'
            }
        }
       
        stage('Build App') {
            steps {
                echo "Building the application..."
                sh "mvn clean package"
            }
        }
       
        stage('Build Docker Image') {
            steps {
                script {
                    echo "Building Docker image..."
                    dockerImage = docker.build("${registry}:${BUILD_NUMBER}")
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    echo "Logging in to Docker Hub..."
                    docker.withRegistry('', registryCredential) {
                        echo "Pushing Docker image to Docker Hub..."
                        dockerImage.push() // Pushes the built image
                    }
                }
            }
        }

        stage('Deploy Docker Container') {
            steps {
                script {
                    echo "Checking for existing container..."
                    // Check if a container with the same name already exists
                    def existingContainer = sh(script: "docker ps -a -q -f name=${CONTAINER_NAME}", returnStdout: true).trim()

                    if (existingContainer) {
                        echo "Stopping and removing the existing container..."
                        sh "docker stop ${CONTAINER_NAME}"
                        sh "docker rm ${CONTAINER_NAME}"
                    }

                    echo "Launching the new container..."
                    sh "docker run --name ${CONTAINER_NAME} -d -p 2222:2222 ${registry}:${BUILD_NUMBER}"
                }
            }
        }
    }

    post {
        success {
            script {
                echo "Pipeline executed successfully!"
            }
        }
        failure {
            script {
                echo "Pipeline execution failed."
            }
        }
    }
}
