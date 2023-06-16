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
                sh './jenkins/scripts/deploy.sh'
                sleep time: 60, unit: 'SECONDS'
            }
        }
    }
}
