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
      stage('Deploy to Kubernetes') {
    steps {
        withCredentials([[
            $class: 'AmazonWebServicesCredentialsBinding',
            credentialsId: 'aws-credentials-id',
            accessKeyVariable: 'AWS_ACCESS_KEY_ID',
            secretKeyVariable: 'AWS_SECRET_ACCESS_KEY'
        ]]) {
            script {
                sh '''
                aws eks --region eu-north-1 update-kubeconfig --name my-eks-cluster
                kubectl apply -f deployment.yaml
                kubectl apply -f service.yaml
                kubectl get pods
                kubectl get services
                '''
            }
        }
    }
}

    }
}
