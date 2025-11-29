pipeline {
    agent any

    environment {
        DOCKER_REPO = "yourdockerhubusername/git-jenkin-docker" // Change this to your DockerHub repo
        DOCKER_CREDS = "dockerhub-creds" // Jenkins credential ID for DockerHub
    }

    stages {
        stage("Pull Code from Git") {
            steps {
                checkout scm
                echo "Code successfully pulled from GitHub"
            }
        }

        stage("Build Docker Image") {
            steps {
                script {
                    IMAGE_TAG = "${DOCKER_REPO}:${env.BUILD_NUMBER}"
                    echo "Building Docker image: ${IMAGE_TAG}"
                    sh "docker build -t ${IMAGE_TAG} ."
                }
            }
        }

        stage("Push Docker Image to DockerHub") {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: "${DOCKER_CREDS}",
                    usernameVariable: "USER",
                    passwordVariable: "PASS"
                )]) {
                    sh """
                        echo "$PASS" | docker login -u "$USER" --password-stdin
                        docker push ${IMAGE_TAG}
                    """
                }
            }
        }

        stage("Run Docker Container (Optional)") {
            steps {
                script {
                    sh """
                        docker stop git-jenkin-docker || true
                        docker rm git-jenkin-docker || true
                        docker run -d --name git-jenkin-docker -p 3000:3000 ${IMAGE_TAG}
                    """
                }
            }
        }
    }

    post {
        success {
            echo "Pipeline completed successfully: ${IMAGE_TAG}"
        }
        failure {
            echo "Pipeline failed! Check logs."
        }
    }
}
