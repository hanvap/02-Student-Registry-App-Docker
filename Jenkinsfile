pipeline {
    agent any

    // Ако използвате Jenkins Node.js plugin, задайте инсталацията тук
    tools {
        nodejs 'NodeJS' // Името на Node.js инсталацията в Jenkins (конфигурира се в Global Tool Configuration)
    }

    stages {
        // Стъпка 1: Checkout на кода от Git хранилище
        stage('Checkout') {
            steps {
                git branch: 'main', 
                url: 'https://github.com/hanvap/02-Student-Registry-App-Docker' // Променете с вашия repository URL
            }
        }

        // Стъпка 2: Проверка на Node.js версията (незадължително, но полезно за диагностика)
        stage('Setup Node.js') {
            steps {
                sh 'node --version'
                sh 'npm --version'
            }
        }

        // Стъпка 3: Инсталиране на зависимости
        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        // Стъпка 4: Стартиране на приложението в background и запазване на PID
        stage('Start Application') {
            steps {
                script {
                    // Стартиране на приложението в background и запазване на PID
                    sh 'nohup npm start & echo $! > app.pid'
                    
                    // Изчакване за готовност (адаптирайте според вашето приложение)
                    sleep(time: 10, unit: 'SECONDS') 
                    // Алтернатива: Проверка с curl за health endpoint
                    // retry(5) { sh 'curl --fail http://localhost:3000' } 
                }
            }
        }

        // Стъпка 5: Изпълнение на тестовете
        stage('Run Tests') {
            steps {
                sh 'npm test' // Или друга команда за тестове (напр. `npm run test:ci`)
            }
            post {
                always {
                    script {
                        // Спиране на приложението след тестове (дори при грешка)
                        sh 'kill $(cat app.pid) || true' // "|| true" предотвратява грешка ако процесът не съществува
                        sh 'rm app.pid || true'
                    }
                }
            }
        }
    }
}
