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

        stage('Release Port 8084') {
            steps {
                script {
                    // Kill process using port 8084 if any
                    sh 'fuser -k 8084/tcp || true'
                }
            }
        }

        stage('Deploy Docker Container') {
            steps {
                script {
                    // Stop and remove any existing containers using the image
                    sh "docker stop \$(docker ps -q --filter ancestor=${DOCKER_HUB_REPO}:${IMAGE_TAG}) || true"
                    sh "docker rm \$(docker ps -a -q --filter ancestor=${DOCKER_HUB_REPO}:${IMAGE_TAG}) || true"
                    // Run the container and bind to a dynamic port
                    sh "docker run -d -p 8084:80 ${DOCKER_HUB_REPO}:${IMAGE_TAG}"
                }
            }
        }

        stage('Wait for Container to Start') {
            steps {
                script {
                    // Wait for 15 seconds to ensure the container is fully up and running
                    sleep(15)
                }
            }
        }

        stage('Test Website Accessibility') {
            steps {
                script {
                    // Test if the site is accessible on port 8084
                    sh 'curl -I http://localhost:8084'
                }
            }
        }

        stage('Check for Broken Links') {
            steps {
                sh 'npm install -g linkinator'  // Install linkinator globally
                sh 'linkinator http://localhost:8084'  // Check for broken links
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}
