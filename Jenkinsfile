pipeline {  
    agent any  

    stages {  
        stage('Setup Node.js') {  
            steps {  
                script {  
                    // Проверка на версията на Node.js  
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
                    // Стартиране на приложението  
                    bat 'start /B npm start'  
                    // Изчакване на приложението да се стартира  
                    bat 'timeout /t 60'  
                    // Проверка дали приложението работи  
                    bat 'timeout /t 1 & curl -s http://localhost:3000 || (echo "Application not reachable" && exit 1)'  
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
                        // Спиране на приложението  
                        bat 'taskkill /IM node.exe /F || true'  
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
