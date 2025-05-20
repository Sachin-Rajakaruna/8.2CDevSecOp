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
                    def message = """
                        <h3>Build Completed</h3>
                        <p><strong>Stage Status:</strong></p>
                        <ul>
                            <li>Run Tests: <b>${testStatus}</b></li>
                            <li>Security Scan: <b>${scanStatus}</b></li>
                        </ul>
                        <p>The full console log is attached.</p>
                    """

                    emailext(
                        to: 'sachinrasmitha@gmail.com',
                        subject: "Build Summary – Tests: ${testStatus}, Scan: ${scanStatus}",
                        body: message,
                        mimeType: 'text/html',
                        attachLog: true
                    )
                }
            }
        }
    }
}
