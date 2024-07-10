pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                // Checkout the code from Git
                git credentialsId: 'git-cred', url: 'https://github.com/Zivgl66/Hpoalim-Laravel-App'
            }
        }

        stage('Install Dependencies') {
            steps {
                // Install Composer dependencies
                sh 'composer install --no-dev --optimize-autoloader'
            }
        }

        stage('Run Tests') {
            steps {
                sh 'php artisan test'
            }
        }

        stage('Build and Deploy') {
            steps {
                sshagent(['ssh-jenkins-agent']) {
                    sh '''
                    ssh -o StrictHostKeyChecking=no ubuntu@3.90.138.232 << EOF
                    cd /path/to/your/laravel/app
                    git pull origin main 
                    composer install --no-dev --optimize-autoloader
                    php artisan migrate --force
                    php artisan optimize
                    sudo systemctl restart apache2  // Or your web server command
                    EOF
                    '''
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed! Check the Jenkins logs for details.'
        }
    }
}