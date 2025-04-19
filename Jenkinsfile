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
                    sh "docker stop \$(docker ps -q --filter ancestor=${DOCKER_HUB_REPO}:${IMAGE_TAG}) || true"
                    sh "docker rm \$(docker ps -a -q --filter ancestor=${DOCKER_HUB_REPO}:${IMAGE_TAG}) || true"
                    sh "docker run -d -p 8084:80 ${DOCKER_HUB_REPO}:${IMAGE_TAG}"
                }
            }
        }

        stage('Lint HTML') {
            steps {
                sh 'npm install -g htmlhint'
                sh 'htmlhint ./*.html'
            }
        }

        stage('Lint CSS') {
            steps {
                sh 'npm install -g stylelint'
                sh 'stylelint "styles/**/*.css"'
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
