pipeline {
    agent any

    stages {
        stage('Setup Node.js') {
            steps {
                script {
                    def nodeVersion = sh(script: 'node --version', returnStdout: true).trim()
                    if (!nodeVersion.startsWith('v16.')) {
                        error("Требуется Node.js v16, текущая версия: ${nodeVersion}")
                    }
                }
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Start Application') {
            steps {
                script {
                    sh 'npm start & echo $! > app.pid'
                    sh 'timeout 60 bash -c "until curl -s http://localhost:3000; do sleep 1; done"'
                }
            }
        }

        stage('Run Tests') {
            steps {
                sh 'npm test'
            }
            post {
                always {
                    script {
                        sh 'pkill -f "npm start" || true'
                        sh 'rm app.pid || true'
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
