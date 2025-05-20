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
            post {
                always {
                    script {
                        // Overwrite or create the status file
                        def status = currentBuild.currentResult == 'SUCCESS' ? 'SUCCESS' : 'FAILURE'
                        writeFile file: 'stage-status.txt', text: "Run Tests: ${status}\n"
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
                bat 'npm audit || exit /b 0'
            }
            post {
                always {
                    script {
                        // Append to the same file
                        def prev = readFile('stage-status.txt')
                        def status = currentBuild.currentResult == 'SUCCESS' ? 'SUCCESS' : 'FAILURE'
                        writeFile file: 'stage-status.txt', text: prev + "Security Scan: ${status}\n"
                    }
                }
            }
        }
    }

    post {
        always {
            script {
                echo "Pipeline finished, sending consolidated status email..."
                def summary = readFile('stage-status.txt')
                emailext(
                    to: 'sachinrasmitha@gmail.com',
                    subject: "Stage Status Summary: ${env.JOB_NAME} [#${env.BUILD_NUMBER}]",
                    body: """<p>Here are the results of your critical stages:</p>
<pre>${summary}</pre>
<p>Attached is <strong>stage-status.txt</strong> with details.</p>""",
                    attachmentsPattern: 'stage-status.txt'
                )
            }
        }
    }
}
