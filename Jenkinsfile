pipeline {
    agent any

    stages {
        stage('Setup Node.js') {
            steps {
                script {
                    def nodeVersion = bat(script: '@node --version', returnStdout: true).trim().replaceAll("[^0-9.]", "")
                    if (!nodeVersion.startsWith('20.')) {
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
                    // Запуск в фоне и сохранение PID
                    bat '''
                        start "App" /B npm start
                        echo %ERRORLEVEL% > app.pid
                    '''
                    
                    // Ожидание готовности приложения
                    bat '''
                        set COUNTER=0
                        :loop
                        timeout /t 1 > nul
                        curl -s http://localhost:3000
                        if %ERRORLEVEL% neq 0 (
                            set /a COUNTER=COUNTER+1
                            if !COUNTER! geq 60 exit 1
                            goto loop
                        )
                    '''
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
                        // Убийство процесса по PID
                        bat '''
                            FOR /F "tokens=*" %%p IN (app.pid) DO (
                                taskkill /PID %%p /F
                            )
                            del app.pid
                        '''
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
