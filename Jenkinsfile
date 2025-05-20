pipeline {
    agent any

    environment {
        TEST_STATUS = 'UNKNOWN'
        SCAN_STATUS = 'UNKNOWN'
    }

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
                script {
                    def result = bat(script: 'npm test', returnStatus: true)
                    if (result == 0) {
                        env.TEST_STATUS = 'SUCCESS'
                    } else {
                        env.TEST_STATUS = 'FAILURE'
                    }
                }
            }
        }

        stage('Generate Coverage Report') {
            steps {
                bat 'npm run coverage || exit /b 0'
            }
        }

        stage('NPM Audit (Security Scan)') {
            steps {
                script {
                    def result = bat(script: 'npm audit', returnStatus: true)
                    if (result == 0) {
                        env.SCAN_STATUS = 'SUCCESS'
                    } else {
                        env.SCAN_STATUS = 'FAILURE'
                    }
                }
            }
        }

        stage('Email Notification') {
            steps {
                script {
                    def msgBody = """
                        <p><b>Build Summary:</b></p>
                        <ul>
                            <li>Run Tests: ${env.TEST_STATUS}</li>
                            <li>Security Scan: ${env.SCAN_STATUS}</li>
                        </ul>
                        <p>Full console log is attached.</p>
                    """
                    currentBuild.description = "Tests: ${env.TEST_STATUS}, Scan: ${env.SCAN_STATUS}"

                    emailext(
                        to: 'sachinrasmitha@gmail.com',
                        subject: "Build Status: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                        body: msgBody,
                        mimeType: 'text/html',
                        attachLog: true
                    )
                }
            }
        }
    }
}
