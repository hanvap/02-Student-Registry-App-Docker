pipeline {
    agent any

    stages {
        stage('Setup Node.js') {
            steps {
                script {
                    def nodeVersion = bat(script: 'node --version', returnStdout: true).trim()
                    if (!nodeVersion.startsWith('v16.')) {
                        error("Требуется Node.js v16, текущая версия: ${nodeVersion}")
                    }
                }
            }
        }

        stage('Install Dependencies') {
            steps {
                bat 'npm install'
            }
        }

        stage('Start Application') {
            steps {
                script {
                    bat 'npm start & echo $! > app.pid'
                    bat 'timeout 60 bash -c "until curl -s http://localhost:3000; do sleep 1; done"'
                }
            }
        }

        stage('Run Tests') {
            steps {
                bat 'npm test'
            }
            post {
                always {
                    script {
                        bat 'pkill -f "npm start" || true'
                        bat 'rm app.pid || true'
                    }
                }
            }
        }
    }

    post {
        always {
            archiveArtifacts(artifacts: 'test-results/**/*', allowEmptyArchive: true)
        }
    }
}
