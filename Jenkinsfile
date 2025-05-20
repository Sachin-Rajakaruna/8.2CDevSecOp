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
                bat 'npm test || exit /b 0'
            }
        }

        stage('Generate Coverage Report') {
            steps {
                bat 'npm run coverage || exit /b 0'
            }
        }

        stage('NPM Audit (Security Scan)') {
            steps {
                bat 'npm audit || exit /b 0'
            }
        }

        stage('Email Notification') {
            // we leave steps empty, all work done in post
            steps {
                echo 'Preparing to send email...'
            }
            post {
                success {
                    emailext(
                        to: 'sachinrasmitha@gmail.com',
                        subject: "✅ Build SUCCEEDED: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                        body: """<p>Your Jenkins build <b>${env.JOB_NAME} #${env.BUILD_NUMBER}</b> completed <b>SUCCESSFULLY</b>.</p>
                                 <p>Please find the attached console log for details.</p>""",
                        attachLog: true
                    )
                }
                failure {
                    emailext(
                        to: 'sachinrasmitha@gmail.com',
                        subject: "❌ Build FAILED: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                        body: """<p>Your Jenkins build <b>${env.JOB_NAME} #${env.BUILD_NUMBER}</b> finished with <b>FAILURE</b>.</p>
                                 <p>Please review the attached console log to diagnose the issue.</p>""",
                        attachLog: true
                    )
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline run complete.'
        }
    }
}
