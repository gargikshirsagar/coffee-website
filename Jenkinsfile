pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/gargikshirsagar/coffee-website.git/'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Run Tests (CT)') {
            steps {
                sh 'npm run test'
            }
        }

        stage('Build (CD)') {
            steps {
                sh 'npm run build'
            }
        }

        stage('Success') {
            steps {
                echo '✅ CI/CD/CT completed!'
            }
        }
    }

    post {
        failure {
            echo '❌ Build failed!'
        }
    }
}
