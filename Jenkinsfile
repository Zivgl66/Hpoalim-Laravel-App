#!groovy
pipeline {
       environment {
        SSH_KEY = credentials('ssh-key-id')
        EC2_INSTANCE = '54.158.42.42'
        APP_PATH = '/home/ubuntu/Hpoalim-Laravel-App'
    }
    agent any

    stages {
        // stage('cleanup') {
        //     steps {
        //         cleanWs()
        //     }
        // }
        stage('checkout') {
            steps {
               checkout scmGit(
                branches: [[name: "main"]],
                userRemoteConfigs: [[credentialsId: 'git-cred',
                url: 'https://github.com/Zivgl66/Hpoalim-Laravel-App.git']]
               )
            }
        }
        // stage('Install Dependencies') {
        //     steps {
        //         // Install Composer dependencies
        //         sh 'composer install --no-dev --optimize-autoloader'
        //     }
        // }

        // stage('Install Dependencies') {
        //     steps {
        //         sh 'composer install --no-ansi --no-interaction --no-progress --optimize-autoloader'
        //     }
        // }

        // stage('Run Tests') {
        //     steps {
        //         sh 'php artisan test'
        //     }
        // }

        stage('Deploy') {
                    steps {
                        withCredentials([sshUserPrivateKey(credentialsId: 'ssh-key-id', keyFileVariable: 'SSH_KEY')]) {
                            sh '''
                                    ssh -o StrictHostKeyChecking=no -i **** ubuntu@54.158.42.42 sudo rm -rf /home/ubuntu/Hpoalim-Laravel-App/*

                                    scp -o StrictHostKeyChecking=no -i **** -r Jenkinsfile README.md app artisan bootstrap composer.json composer.lock config database package.json phpunit.xml public resources routes storage tests txt.txt vite.config.js ubuntu@54.158.42.42:/home/ubuntu/Hpoalim-Laravel-App/

                                    ssh -o StrictHostKeyChecking=no -i **** ubuntu@54.158.42.42 "cd /home/ubuntu/Hpoalim-Laravel-App && sudo composer install --no-ansi --no-interaction --no-progress --optimize-autoloader && sudo chown -R ubuntu:ubuntu storage bootstrap/cache"
                                // ssh -o StrictHostKeyChecking=no -i $SSH_KEY ubuntu@$EC2_INSTANCE "sudo rm -rf $APP_PATH/*"
                                // scp -o StrictHostKeyChecking=no -i $SSH_KEY -r * ubuntu@$EC2_INSTANCE:$APP_PATH/
                                // ssh -o StrictHostKeyChecking=no -i $SSH_KEY ubuntu@$EC2_INSTANCE "cd $APP_PATH && sudo composer install --no-ansi --no-interaction --no-progress --optimize-autoloader && sudo chown -R ubuntu:ubutnu storage bootstrap/cache"
                                // ssh -o StrictHostKeyChecking=no -i $SSH_KEY ubuntu@$EC2_INSTANCE "sudo systemctl restart apache2.service"
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

