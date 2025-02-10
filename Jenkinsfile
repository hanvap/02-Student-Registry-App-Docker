pipeline {
    agent any

   

    stages {
        // Автоматичен checkout се извършва от Jenkins (не е нужен ръчен stage)

        stage('Setup Node.js') {
            steps {
                sh 'node --version'
                sh 'npm --version'
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
                    sh 'nohup npm start & echo $! > app.pid'
                    sleep(time: 10, unit: 'SECONDS')
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
                        sh 'kill $(cat app.pid) || true'
                        sh 'rm app.pid || true'
                    }
                }
            }
        }
    }
}
