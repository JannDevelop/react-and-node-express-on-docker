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
                echo 'Собираем Docker-образы...'
                sh 'docker compose build'
            }
        }

        stage('3. Deploy Application') {
            steps {
                echo 'Перезапускаем сервисы...'
                sh 'docker compose down'
                sh 'docker compose up -d'
            }
        }

        stage('4. Health Check') {
            steps {
                echo 'Проверяем доступность сервисов...'
                sleep time: 5, unit: 'SECONDS'
                
                sh '''
                    # Получаем IP-адрес шлюза (хоста Ubuntu) в сети Compose
                    HOST_IP=$(docker inspect react-express-pipeline-client-1 --format '{{range .NetworkSettings.Networks}}{{.Gateway}}{{end}}')
                    
                    echo "Хост найден по IP: $HOST_IP"
                    
                    # Делаем запросы через временный контейнер к IP хоста
                    docker run --rm curlimages/curl --retry 5 --retry-delay 2 --retry-connrefused -I "http://$HOST_IP:3000"
                    docker run --rm curlimages/curl --retry 5 --retry-delay 2 --retry-connrefused -I "http://$HOST_IP:5000"
                '''
            }
        }
    }

    post {
        success {
            echo '✅ Деплой успешно завершен! Приложение работает.'
        }
        failure {
            echo '❌ Ошибка при сборке или деплое.'
        }
    }
}
