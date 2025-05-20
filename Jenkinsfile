def testStatus = 'UNKNOWN'
def scanStatus = 'UNKNOWN'

pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Sachin-Rajakaruna/8.2CDevSecOp.git'
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
                    testStatus = (result == 0) ? 'SUCCESS' : 'FAILURE'
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
                    scanStatus = (result == 0) ? 'SUCCESS' : 'FAILURE'
                }
            }
        }

        stage('Email Notification') {
            steps {
                script {
                    def message = """\
Build Completed.

Stage Status:
- Run Tests: ${testStatus}
- Security Scan: ${scanStatus}

Note: Console logs are not attached.
"""
                    mail to: 'sachinrasmitha@gmail.com',
                         subject: "Build Summary – Tests: ${testStatus}, Scan: ${scanStatus}",
                         body: message
                }
            }
        }
    }
}
