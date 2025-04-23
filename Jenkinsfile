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
                    // Stop and remove any container already using the port
                    sh 'docker ps -q --filter "publish=8082" | xargs -r docker stop'
                    sh 'docker ps -a -q --filter "publish=8082" | xargs -r docker rm'

                    // Run container
                    sh "docker run -d -p 8082:80 ${DOCKER_HUB_REPO}:${IMAGE_TAG}"
                }
            }
        }

        stage('CT') {
            steps {
                sh '''
                    echo "👉 Running HTML & CSS Lint Check..."

                    # Install necessary linters if not already installed
                    npm install -g htmlhint stylelint stylelint-config-standard

                    # Use a basic config for stylelint if missing
                    echo '{ "extends": "stylelint-config-standard" }' > .stylelintrc.json

                    # Lint HTML (non-breaking)
                    htmlhint "**/*.html" 

                    # Lint CSS and auto-fix (non-breaking)
                    stylelint "**/*.css" --fix 

                    echo "✅ Done. CT stage completed successfully."
                '''
            }
        }
    }

    post {
        always {
            cleanWs() // Clean workspace after execution
        }
    }
}
