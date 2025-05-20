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
                    def testStatus = bat(script: 'npm test > email_noti_tests.txt 2>&1', returnStatus: true)
                    def result = testStatus == 0 ? "SUCCESS" : "FAILURE"
                    emailext(
                        to: 'sachinrasmitha@gmail.com',
                        subject: "Test Stage Result: ${result}",
                        body: "The 'Run Tests' is completed with status: ${result}.\nCheck the attached test log.",
                        attachmentsPattern: 'email_noti_tests.txt'
                    )
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
                    def auditStatus = bat(script: 'npm audit > email_noti_audit.txt 2>&1', returnStatus: true)
                    def result = auditStatus == 0 ? "SUCCESS" : "FAILURE"
                    emailext(
                        to: 'sachinrasmitha@gmail.com',
                        subject: "Security Scan Stage Result: Audit Status: ${result}",
                        body: "The 'Security Scan' is completed with status: ${result}.\nCheck the attached audit log.",
                        attachmentsPattern: 'email_noti_audit.txt'
                    )
                }
            }
        }
    }
}
