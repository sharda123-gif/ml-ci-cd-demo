pipeline {
    agent any

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/sharda123-gif/ml-ci-cd-demo.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh 'docker build -t my-ml-app .'
                }
            }
        }

        stage('Push to Docker Hub') {
            environment {
                DOCKER_USER = credentials('docker-hub-username')
                DOCKER_PASS = credentials('docker-hub-password')
            }
            steps {
                script {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                    sh 'docker tag my-ml-app $DOCKER_USER/my-ml-app:latest'
                    sh 'docker push $DOCKER_USER/my-ml-app:latest'
                }
            }
        }

        stage('Deploy to EC2') {
            steps {
                sshagent (credentials: ['ec2-ssh-key']) {
                    sh '''
                    ssh -o StrictHostKeyChecking=no ubuntu@your-ec2-instance-ip <<EOF
                    docker pull docker-hub-username/my-ml-app:latest
                    docker stop my-ml-app || true
                    docker rm my-ml-app || true
                    docker run -d -p 5000:5000 --name my-ml-app docker-hub-username/my-ml-app:latest
                    EOF
                    '''
                }
            }
        }
    }
}
