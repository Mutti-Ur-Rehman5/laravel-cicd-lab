
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

        stage('Deploy Locally') {
            steps {
                sh '''
                    DEPLOY_DIR=/var/www/laravel-app

                    rsync -a \
                        --exclude='.git/' \
                        --exclude='.env' \
                        --exclude='storage/' \
                        --exclude='bootstrap/cache/' \
                        ./ "$DEPLOY_DIR/"

                    cd "$DEPLOY_DIR"

                    if [ ! -f .env ]; then
                        cp .env.example .env
                    fi

                    mkdir -p database
                    touch database/database.sqlite

                    mkdir -p storage/framework/cache
                    mkdir -p storage/framework/sessions
                    mkdir -p storage/framework/views
                    mkdir -p storage/logs
                    mkdir -p bootstrap/cache

                    chgrp -R www-data storage bootstrap/cache
                    chmod -R g+rwX storage bootstrap/cache
                    find storage bootstrap/cache -type d -exec chmod g+s {} \\;

                    if ! grep -q '^APP_KEY=base64:' .env; then
                        php artisan key:generate --force
                    fi

                    php artisan config:clear
                '''
            }
        }
    }
}

