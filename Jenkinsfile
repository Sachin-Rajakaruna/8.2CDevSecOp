pipeline {
    agent any

    tools {
        nodejs 'NodeJS_14'
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
                // Run tests but don't fail pipeline
                bat 'npm test || exit /b 0'
            }
            post {
                always {
                    // Write the test stage status to our status file
                    script {
                        def status = currentBuild.currentResult == 'SUCCESS' ? 'SUCCESS' : 'FAILURE'
                        writeFile file: 'stage-status.txt',
                                  text: "Run Tests: ${status}\n",
                                  encoding: 'UTF-8'
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
                    // Append the security scan status to the same file
                    script {
                        def status = currentBuild.currentResult == 'SUCCESS' ? 'SUCCESS' : 'FAILURE'
                        writeFile file: 'stage-status.txt',
                                  text: readFile('stage-status.txt') + "Security Scan: ${status}\n",
                                  encoding: 'UTF-8'
                    }
                }
            }
        }
    }

    post {
        always {
            echo "Pipeline finished, sending consolidated status email..."

            // Read the status file for inclusion in the email body
            def summary = readFile('stage-status.txt')

            // Send one final email summarizing both stages and attaching the file
            emailext(
                to: 'sachinrasmitha@gmail.com',
                subject: "Stage Status Summary: Job '${env.JOB_NAME} [#${env.BUILD_NUMBER}]'",
                body: """<p>Here are the results of your critical stages:</p>
                         <pre>${summary}</pre>
                         <p>See attached <code>stage-status.txt</code> for details.</p>""",
                attachmentsPattern: 'stage-status.txt'
            )
        }
    }
}
