pipeline {
    agent any

    environment {
        registry = "wahidh007/demo-jenkins"
        registryCredential = 'docker-hub-credentials'
        dockerImage = ''        
        CONTAINER_NAME = 'demo-jenkins'
    }

    triggers {
        pollSCM('*/5 * * * *')
    }

    stages {
        stage('Clone Repo') {
            steps {
                git branch: 'main', url: 'https://github.com/wahid007/Jenkins-Demo.git'
            }
        }
       
        stage('Build App') {
            steps {
                sh "mvn clean package"
            }
        }
       
        stage('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build("${registry}:${BUILD_NUMBER}")
                }
            }
        }      

        stage('Deploy Docker Container') {
            steps {
                script {
                    // Vérifier si un conteneur avec le même nom existe
                    def existingContainer = sh(script: "docker ps -a -q -f name=${CONTAINER_NAME}", returnStdout: true).trim()

                    // Si un conteneur est trouvé, on l'arrête et on le supprime
                    if (existingContainer) {
                        echo "Conteneur existant trouvé : ${existingContainer}. Arrêt et suppression en cours..."
                        sh "docker stop ${existingContainer}"
                        sh "docker rm ${existingContainer}"
                    }

                    // Lancer le nouveau conteneur
                    echo "Lancement du nouveau conteneur Docker..."
                    sh "docker run --name ${CONTAINER_NAME} -d -p 2222:2222 ${registry}:${BUILD_NUMBER}"
                }
            }
        }
    }

    post {
        success {
            script {
                echo 'Pipeline execution successful!'
                // Notification Slack supprimée
                // try {
                //     slackSend(
                //         channel: '#your-slack-channel', 
                //         color: "good", 
                //         message: "Pipeline réussi ! Image déployée : ${registry}:${BUILD_NUMBER} :tada:"
                //     )
                // } catch (Exception e) {
                //     echo "Notification Slack échouée : ${e.message}"
                // }
            }
        }
        failure {
            script {
                echo 'Pipeline execution failed.'
                // Notification Slack supprimée
                // try {
                //     slackSend(
                //         channel: '#your-slack-channel', 
                //         color: "danger", 
                //         message: "Échec de la pipeline. Veuillez vérifier :cry:"
                //     )
                // } catch (Exception e) {
                //     echo "Notification Slack échouée : ${e.message}"
                // }
            }
        }
    }
}
