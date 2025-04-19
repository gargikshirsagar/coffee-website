pipeline {
    agent any

    environment {
        DOCKER_HUB_REPO = 'gargimk/coffee-website'
        GIT_REPO = 'https://github.com/gargikshirsagar/coffee-website.git'
        IMAGE_NAME = 'coffee-website'
        IMAGE_TAG = 'latest'
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: "${GIT_REPO}"
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker build -t ${DOCKER_HUB_REPO}:${IMAGE_TAG} ."
                }
            }
        }

        stage('Login & Push Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                    script {
                        sh 'echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USERNAME" --password-stdin'
                        sh "docker push ${DOCKER_HUB_REPO}:${IMAGE_TAG}"
                    }
                }
            }
        }

        stage('Deploy Docker Container') {
            steps {
                script {
                    // Optional: gracefully stop existing container
                    sh "docker stop \$(docker ps -q --filter ancestor=${DOCKER_HUB_REPO}:${IMAGE_TAG}) || true"
                    sh "docker rm \$(docker ps -a -q --filter ancestor=${DOCKER_HUB_REPO}:${IMAGE_TAG}) || true"

                    // Run new container
                    sh "docker run -d -p 8082:80 ${DOCKER_HUB_REPO}:${IMAGE_TAG}"
                }
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}
