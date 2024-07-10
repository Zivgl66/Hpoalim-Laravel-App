// #!groovy
// pipeline {
//        environment {
//         // Set your environment variables here
//         AWS_ACCESS_KEY_ID = credentials('aws-access-key-id')
//         AWS_SECRET_ACCESS_KEY = credentials('aws-secret-access-key')
//         SSH_KEY = credentials('ssh-private-key')
//         EC2_INSTANCE = '54.158.42.42'
//         APP_PATH = '/var/www/html' // Path where your Laravel app will be deployed on EC2
//     }
//     agent any

//     stages {
//         // stage('cleanup') {
//         //     steps {
//         //         cleanWs()
//         //     }
//         // }
//         stage('checkout') {
//             steps {
//                checkout scmGit(
//                 branches: [[name: "main"]],
//                 userRemoteConfigs: [[credentialsId: 'git-cred',
//                 url: 'https://github.com/Zivgl66/Hpoalim-Laravel-App.git']]
//                )
//             }
//         }
//         // stage('Install Dependencies') {
//         //     steps {
//         //         // Install Composer dependencies
//         //         sh 'composer install --no-dev --optimize-autoloader'
//         //     }
//         // }

//         // stage('Run Tests') {
//         //     steps {
//         //         sh 'php artisan test'
//         //     }
//         // }

//         stage('Build and Deploy') {
//             steps {
//                 sshagent(['ssh-jenkins-agent']) {
//                     sh '''
//                     ssh -o StrictHostKeyChecking=no ubuntu@3.90.138.232 << EOF
//                     cd /path/to/your/laravel/app
//                     git pull origin main 
//                     composer install --no-dev --optimize-autoloader
//                     php artisan migrate --force
//                     php artisan optimize
//                     sudo systemctl restart apache2  // Or your web server command
//                     EOF
//                     '''
//                 }
//             }
//         }
//     }

//     post {
//         success {
//             echo 'Pipeline completed successfully!'
//         }
//         failure {
//             echo 'Pipeline failed! Check the Jenkins logs for details.'
//         }
//     }
// }


pipeline {
    agent any

    environment {
        AWS_ACCESS_KEY_ID = credentials('aws-access-key-id')
        AWS_SECRET_ACCESS_KEY = credentials('aws-secret-access-key')
        SSH_KEY = credentials('ssh-private-key')
        EC2_INSTANCE = '54.158.42.42'
    }

    stages {
    stage('checkout') {
             steps {
                checkout scmGit(
                 branches: [[name: "main"]],
                userRemoteConfigs: [[credentialsId: 'git-cred',
                url: 'https://github.com/Zivgl66/Hpoalim-Laravel-App.git']]
                )
           }
        }

        stage('Install Dependencies') {
            steps {
                sh 'composer install --no-ansi --no-interaction --no-progress --optimize-autoloader'
            }
        }

        stage('Deploy') {
            steps {
                withCredentials([sshUserPrivateKey(credentialsId: 'ssh-key-id', keyFileVariable: 'SSH_KEY')]) {
                    sh '''
                        ssh -o StrictHostKeyChecking=no -i $SSH_KEY ubuntu@$EC2_INSTANCE "sudo rm -rf $APP_PATH/*"
                        scp -o StrictHostKeyChecking=no -i $SSH_KEY -r * ubuntu@$EC2_INSTANCE:$APP_PATH/
                        ssh -o StrictHostKeyChecking=no -i $SSH_KEY ubuntu@$EC2_INSTANCE "cd $APP_PATH && sudo composer install --no-ansi --no-interaction --no-progress --optimize-autoloader && sudo chown -R apache:apache storage bootstrap/cache"
                        ssh -o StrictHostKeyChecking=no -i $SSH_KEY ubuntu@$EC2_INSTANCE "sudo systemctl restart apache2.service"
                    '''
                }
            }
        }
    }
}