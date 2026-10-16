pipeline {
    agent any

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
                    # Получаем имя сети
                    NETWORK_NAME=$(docker inspect react-express-pipeline-client-1 --format '{{range $k, $v := .NetworkSettings.Networks}}{{$k}}{{end}}')
                    
                    # Извлекаем IP-адрес контейнера клиента напрямую из сети
                    CLIENT_IP=$(docker inspect react-express-pipeline-client-1 --format '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}')
                    
                    echo "Используется сеть: ${NETWORK_NAME}"
                    echo "IP клиента: ${CLIENT_IP}"

                    # Проверка клиентской части по IP-адресу контейнера
                    docker run --rm --network "${NETWORK_NAME}" curlimages/curl \
                      --retry 5 --retry-delay 2 --retry-connrefused -I "http://${CLIENT_IP}:3000"
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
