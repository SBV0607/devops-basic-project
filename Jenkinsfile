pipeline {
    agent any

    environment {
        EC2_HOST = "ubuntu@16.171.54.151"
    }

    stages {

        stage('Clone Repo') {
            steps {
                git branch: 'main', url: 'https://github.com/SBV0607/devops-basic-project.git'
            }
        }

        stage('Build Docker Image (Jenkins)') {
            steps {
                sh 'docker build -t myapp .'
            }
        }

        stage('Copy App to EC2') {
            steps {
                sshagent(['ec2-ssh-key']) {

                    sh '''
                    # Create app directory on EC2
                    ssh -o StrictHostKeyChecking=no ubuntu@16.171.54.151 "mkdir -p /home/ubuntu/app"

                    # Copy only necessary files (avoid copying .git folder)
                    scp -o StrictHostKeyChecking=no app.py ubuntu@16.171.54.151:/home/ubuntu/app/
                    scp -o StrictHostKeyChecking=no Dockerfile ubuntu@16.171.54.151:/home/ubuntu/app/
                    scp -o StrictHostKeyChecking=no requirements.txt ubuntu@16.171.54.151:/home/ubuntu/app/
                    '''
                }
            }
        }

        stage('Run App on EC2') {
            steps {
                sshagent(['ec2-ssh-key']) {
                    sh '''
                    ssh -o StrictHostKeyChecking=no ubuntu@16.171.54.151 "
                        cd /home/ubuntu/app &&

                        # Stop old container if exists
                        docker stop myapp || true &&
                        docker rm myapp || true &&

                        # Build docker image
                        docker build -t myapp . &&

                        # Run the container
                        docker run -d -p 5000:5000 --name myapp myapp
                    "
                    '''
                }
            }
        }

    }
}
