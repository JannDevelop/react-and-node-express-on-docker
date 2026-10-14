pipeline {
    agent any

    environment {
        // Указываем название проекта для именования контейнеров
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
                // Здесь можно добавить команды для прогона тестов, например:
                // sh 'docker compose run --rm server npm test'
            }
        }

        stage('4. Deploy Application') {
            steps {
                echo 'Перезапускаем сервисы на стенде...'
                // Останавливаем старые контейнеры и запускаем новые в фоновом режиме
                sh 'docker compose down'
                sh 'docker compose up -d'
            }
        }

        stage('5. Health Check') {
            steps {
                echo 'Проверяем доступность сервисов...'
                // Проверяем, что сервисы отвечают по HTTP
                sh 'curl -I http://localhost:3000'
                sh 'curl -I http://localhost:5000'
            }
        }
    }

    post {
        success {
            echo '✅ Деплой успешно завершен! Приложение обновлено.'
        }
        failure {
            echo '❌ Ошибка при сборке или деплое. Проверьте логи выше.'
        }
    }
}
