pipeline {
    agent any

    environment {
        REPO = "mubeentahir12345/git-jenkin-docker"  // tumhara DockerHub username
        CREDS = "dockerhub-creds"                    // Jenkins credential ID
    }

    stages {
        stage("Pull from Git") {
            steps {
                checkout scm
                echo "Code pulled from GitHub"
            }
        }

        stage("Build Docker Image") {
            steps {
                script {
                    IMAGE_TAG = "${REPO}:${env.BUILD_NUMBER}"
                    sh "docker build -t ${IMAGE_TAG} ."
                    echo "Docker image built: ${IMAGE_TAG}"
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
                    sh '''
                        echo $PASS | docker login -u $USER --password-stdin
                        docker push ${IMAGE_TAG}
                    '''
                }
            }
        }

        stage("Run Docker Container (Optional)") {
            steps {
                script {
                    sh '''
                        docker stop git-jenkin-docker || true
                        docker rm git-jenkin-docker || true
                        docker run -d --name git-jenkin-docker -p 3000:3000 ${IMAGE_TAG}
                    '''
                }
            }
        }
    }

    post {
        success { echo "Pipeline completed successfully!" }
        failure { echo "Pipeline failed!" }
    }
}
