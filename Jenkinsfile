pipeline {
    agent any

    environment {
        EC2_HOST = "ubuntu@13.48.178.159"
    }

    stages {
        stage('Clone Repo') {
            steps {
                git 'https://github.com/SBV0607/devops-basic-project.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t myapp .'
            }
        }

        stage('Copy App to EC2') {
            steps {
                sshagent(['ec2-ssh-key']) {
                    sh '''
                    scp -o StrictHostKeyChecking=no -r . ubuntu@13.48.178.159:/home/ubuntu/app
                    '''
                }
            }
        }

        stage('Run App on EC2') {
            steps {
                sshagent(['ec2-ssh-key']) {
                    sh '''
                    ssh -o StrictHostKeyChecking=no $EC2_HOST "
                        cd app &&
                        docker stop myapp || true &&
                        docker rm myapp || true &&
                        docker build -t myapp . &&
                        docker run -d -p 5000:5000 --name myapp myapp
                    "
                    '''
                }
            }
        }
    }
}
