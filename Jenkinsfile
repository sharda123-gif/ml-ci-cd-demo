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
          environment {
              AWS_CRED = credentials('aws-credentials-id')  // AWS IAM credentials stored in Jenkins
          }
          steps {
              script {
                  // Configure AWS credentials
                  sh '''
                  export AWS_ACCESS_KEY_ID=$AWS_CRED_USR
                  export AWS_SECRET_ACCESS_KEY=$AWS_CRED_PSW
                  aws eks --region your-region update-kubeconfig --name my-eks-cluster
                  '''

                  // Deploy to EKS
                  sh '''
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
