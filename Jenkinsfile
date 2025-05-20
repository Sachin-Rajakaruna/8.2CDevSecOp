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
                    // Use script-level variable for later reference
                    if (!binding.hasVariable('testStatus')) {
                        binding.setVariable('testStatus', 'UNKNOWN')
                    }

                    def result = bat(script: 'npm test', returnStatus: true)
                    if (result == 0) {
                        testStatus = 'SUCCESS'
                    } else {
                        testStatus = 'FAILURE'
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
                    if (!binding.hasVariable('scanStatus')) {
                        binding.setVariable('scanStatus', 'UNKNOWN')
                    }

                    def result = bat(script: 'npm audit', returnStatus: true)
                    if (result == 0) {
                        scanStatus = 'SUCCESS'
                    } else {
                        scanStatus = 'FAILURE'
                    }
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
