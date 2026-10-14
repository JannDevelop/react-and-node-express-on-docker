pipeline {
    agent any

    environment {
        PROJECT_NAME = 'react-node-app'
    }

    stages {
        stage('1. Checkout Code') {
            steps {
                echo 'Получаем свежий код из Git-репозитория...'
                checkout scm
            }
        }

        stage('2. Build Containers') {
            steps {
                echo 'Собираем Docker-образы для Client и Server...'
                sh 'docker compose build'
            }
        }

        stage('3. Run Tests (Optional)') {
            steps {
                echo 'Запускаем автоматические тесты...'
                // sh 'docker compose run --rm server npm test'
            }
        }

        stage('4. Deploy Application') {
            steps {
                echo 'Перезапускаем сервисы на стенде...'
                sh 'docker compose down'
                sh 'docker compose up -d'
            }
        }

        stage('5. Health Check') {
            steps {
                echo 'Проверяем доступность сервисов...'
                // Даем контейнерам время на инициализацию процессов
                sleep time: 7, unit: 'SECONDS'

                // Проверяем доступность через IP Docker-моста хоста (172.17.0.1)
                sh 'curl -I http://172.17.0.1:3000'
                sh 'curl -I http://172.17.0.1:5000'
            }
        }
    }

    post {
        success {
            echo '✅ Деплой успешно завершен! Приложение доступно на портах 3000 и 5000.'
        }
        failure {
            echo '❌ Ошибка при сборке или деплое. Проверьте логи выше.'
        }
    }
}
