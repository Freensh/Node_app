pipeline {
    agent any

    environment {
        backendRegistry = ''
        frontendRegistry = ''
        registryCredential = 'challenge'
        backendImage = ''
        frontendImage = ''
    }

    stages {
        stage ('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Freensh/Node-challenge-backend.git'
            }
        }

        stage ('Installing depencies'){
            steps {
                sh 'npm install'
            }
        }
        stage('Buiding backend container'){
            steps {
                dir(./backend/)
                backendImage = docker.build backendRegistry + ":$BUILD_NUMBER"
            }
        }
        stage ('Buiilding frontend image') {
            steps {
                dir(./frontend/)
                frontendImage = docker.build frontendRegistry + ":$BUILD_NUMBER"
            }
        }
        stage ('Pushing Backend Image to ECR'){
            steps {
                script {
                    docker.withRegistry("https://"+backendRegistry, "ecr:us-east-1:"+registryCredential){
                        backendImage.push()
                    }
                }
            }
        }
        stage ('Pushing Frontend Image to ECR'){
            steps {
                script {
                    docker.withRegistry("https://"+frontendRegistry, "ecr:us-east-1:"+ registryCredential){
                        frontendImage.push()
                    }
                }
            }
        }
        stage ('Initialysing the terraform code'){
            steps{
                
                sh 'terraform init'
            }
        }
        stage ('Deploying tthe app to ECS'){
            steps{
                sh 'terraform apply --auto-approve -var="image_tag=$BUILD_NUMBER"'
            }
        }
    }
}