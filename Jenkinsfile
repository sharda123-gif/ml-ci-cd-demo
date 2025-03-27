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
                DOCKER_CRED = credentials('docker-hub-username')
           }
           steps {
               script {
                  sh 'echo $DOCKER_CRED_PSW | docker login -u $DOCKER_CRED_USR --password-stdin'
                  sh 'docker tag my-ml-app $DOCKER_CRED_USR/my-ml-app:latest'
                  sh 'docker push $DOCKER_CRED_USR/my-ml-app:latest'
               }
           }
      }
    }
}

