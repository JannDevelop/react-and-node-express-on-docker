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
                    # Получаем имя созданной сети Compose
                    NETWORK_NAME=$(docker inspect react-express-pipeline-client-1 --format '{{range $k, $v := .NetworkSettings.Networks}}{{$k}}{{end}}')
                    
                    echo "Проверяем сервисы в сети: ${NETWORK_NAME}"

                    # Проверяем клиент (React) по имени сервиса в Compose
                    docker run --rm --network "${NETWORK_NAME}" curlimages/curl \
                      --retry 5 --retry-delay 2 --retry-connrefused -I http://react-express-pipeline-client-1:3000 || \
                    docker run --rm --network "${NETWORK_NAME}" curlimages/curl \
                      --retry 5 --retry-delay 2 --retry-connrefused -I http://client:3000
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
