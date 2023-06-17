pipeline {
    agent {
        docker {
            image 'node:16-buster-slim'
            args '-p 3000:3000'
        }
    }
    environment {
        HOME = '.'
    }
    stages {
        stage('Build') {
            steps {
                sh 'npm install'
            }
        }
        stage('Test') {
            steps {
                sh './jenkins/scripts/test.sh'
            }
        }
        stage('Manual Approval') {
            steps {
                script {
                    userInput = input(
                        id: 'userInput',
                        message: 'Lanjutkan ke tahap Deploy?',
                        parameters: [
                            choice(choices: ['Proceed', 'Abort'], description: 'Pilih opsi untuk melanjutkan atau menghentikan pipeline ke tahap Deploy', name: 'approval')
                        ]
                    )
                    if (params['approval'] == 'Abort') {
                        error('Pipeline dihentikan oleh pengguna')
                    }
                }
            }
        }
        stage('Deploy') {
            steps {
                sh './jenkins/scripts/deliver.sh' 
                sleep time: 1, unit: 'MINUTES'
                sh './jenkins/scripts/kill.sh' 
            }
        }
        // stage('Deploy Cloud') {
        //     environment {
        //         AWS_ACCESS_KEY_ID = credentials('AKIAVK7F3DYUO5FG3ALC') // Menambahkan credential untuk AWS Access Key ID
        //         AWS_SECRET_ACCESS_KEY = credentials('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') // Menambahkan credential untuk AWS Secret Access Key
        //     }
        //     steps {
        //         withCredentials([string(credentialsId: 'ap-southeast-1', variable: 'AWS_REGION')]) { // Menambahkan credential untuk AWS Region
        //             sh '''
        //                 aws configure set aws_access_key_id "$AWS_ACCESS_KEY_ID"
        //                 aws configure set aws_secret_access_key "$AWS_SECRET_ACCESS_KEY"
        //                 aws configure set region "$AWS_REGION"
        //                 ./jenkins/scripts/deploy.sh
        //             '''
        //         }
        //     }
        // }
    }
}
