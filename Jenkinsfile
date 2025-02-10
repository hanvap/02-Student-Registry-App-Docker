pipeline {  
    agent any  
    
    stages {  
        stage('Checkout Code') {  
            steps {  
                // Извличане на кода от версията за контрол  
                git url: 'https://github.com/hanvap/02-Student-Registry-App-Docker', branch: 'main'  
            }  
        }  

        stage('Setup Node.js') {  
            steps {  
                // Настройване на Node.js среда  
                script {  
                    def nodeVersion = '14' // Заменете с вашата версия на Node.js  
                    env.NODE_VERSION = nodeVersion  
                }  
                // Инсталиране на Node.js  
                sh 'curl -fsSL https://deb.nodesource.com/setup_${NODE_VERSION}.x | sudo -E bash -'  
                sh 'sudo apt-get install -y nodejs'  
            }  
        }  

        stage('Install Dependencies') {  
            steps {  
                // Инсталиране на зависимостите  
                sh 'npm install' // Или `yarn install`, в зависимост от вашия пакетен мениджър  
            }  
        }  

        stage('Start Application') {  
            steps {  
                // Стартиране на приложението  
                sh 'npm start &'  
                // Може да добавите команда за изчакване, ако е необходимо  
            }  
        }  

        stage('Run Tests') {  
            steps {  
                // Изпълнение на тестовете  
                sh 'npm test' // Или друга команда в зависимост от вашата конфигурация  
            }  
        }  
    }  

    post {  
        always {  
            // Действия, които да се изпълняват след всяко стартиране на pipeline  
            echo 'Pipeline finished.'  
        }  
        success {  
            echo 'Build was successful!'  
        }  
        failure {  
            echo 'Build failed.'  
        }  
    }  
}
