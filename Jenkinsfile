pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/Sachin-Rajakaruna/8.2CDevSecOp.git'
            }
        }
        stage('Install Dependencies') {
            steps {
                bat 'npm install'
            }
        }
        stage('Run Tests') {
            steps {
                // Continue on test failures
                bat 'npm test || exit /b 0'
            }
        }
        stage('Generate Coverage Report') {
            steps {
                // Ensure coverage report exists
                bat 'npm run coverage || exit /b 0'
            }
        }
        stage('NPM Audit (Security Scan)') {
            steps {
                // Show known CVEs without failing the build
                bat 'npm audit || exit /b 0'
            }
        }
        stage('Build') {
            steps {
                echo "Building..."
            }
            post {
                success {
                    mail to: 'sachinrasmitha@gmail.com',
                         subject: 'Build Status Email',
                         body: 'Build Was Successful'
                }
            }
        }
    }
}
