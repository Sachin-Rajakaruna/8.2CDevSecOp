pipeline {
    agent any

    environment {
        TEST_STATUS = 'UNKNOWN'
        SCAN_STATUS = 'UNKNOWN'
    }

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
                    def message = """
<p><strong>Build Completed</strong></p>
<ul>
    <li>Run Tests: <b>${env.TEST_STATUS}</b></li>
    <li>Security Scan: <b>${env.SCAN_STATUS}</b></li>
</ul>
<p>See attached console log for more information.</p>
"""

                    emailext(
                        to: 'sachinrasmitha@gmail.com',
                        subject: "Build Summary – Tests: ${env.TEST_STATUS}, Scan: ${env.SCAN_STATUS}",
                        body: message,
                        mimeType: 'text/html',
                        attachLog: true
                    )
                }
            }
        }
    }
}
