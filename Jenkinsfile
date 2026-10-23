
pipeline {
    agent any

    stages {
        stage('Install Dependencies') {
            steps {
                sh 'composer install --no-interaction'
            }
        }

        stage('Prepare Laravel') {
            steps {
                sh '''
                    if [ ! -f .env ]; then
                        cp .env.example .env
                    fi

                    mkdir -p database
                    touch database/database.sqlite

                    php artisan key:generate
                '''
            }
        }

        stage('Test Laravel') {
            steps {
                sh 'php artisan test'
            }
        }
    }
}
