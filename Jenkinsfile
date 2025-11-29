pipeline {
    agent any

    environment {
        REPO = "yourdockerhubusername/git-jenkin-docker"
        CREDS = "dockerhub-creds"
    }

    stages {
        stage("Pull from Git") {
            steps {
                checkout scm
            }
        }

        stage("Build Docker Image") {
            steps {
                script {
                    IMAGE_TAG = "${REPO}:${env.BUILD_NUMBER}"
                    sh "docker build -t ${IMAGE_TAG} ."
                }
            }
        }

        stage("Push to DockerHub") {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: CREDS,
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
    }
}
