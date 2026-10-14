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
                sleep time: 7, unit: 'SECONDS'

                // Запускаем минимальный контейнер curl прямо в сети приложения
                sh 'docker run --rm --network react-express-pipeline_default curlimages/curl -I http://client:3000'
                sh 'docker run --rm --network react-express-pipeline_default curlimages/curl -I http://server:5000'
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
