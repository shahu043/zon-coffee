pipeline {
    agent any  
    environment {
         AWS_ACCOUNT_ID="183465517923"
         AWS_DEFAULT_REGION="us-east-1" 
         IMAGE_REPO_NAME="zoncoffee"
         IMAGE_TAG="latest"
         REPOSITORY_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}"
    }
    
    stages {
        stage('Logging into AWS ECR') {
          steps {
           script {
              sh "aws ecr get-login-password - region ${AWS_DEFAULT_REGION} | docker login - username AWS - password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com"
           }
 
         }
        }
        stage('Cloning Git') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'github-credentials', url: 'https://github.com/shahu043/zon-coffee.git']]])     
            }
        }
        
            // Building Docker images
        stage('Building image') {
          steps{
            script {
              sh 'docker build -t $JOB_NAME:v1.$BUILD_ID .'
              sh 'docker tag $JOB_NAME:v1.$BUILD_ID ${registry}:v1.$BUILD_ID'
             }
           }
         }
   
    // Uploading Docker images into AWS ECR
       stage('Pushing to ECR') {
         steps{  
           script {
             sh 'aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 183465517923.dkr.ecr.us-east-1.amazonaws.com' 
             sh 'docker push ${registry}:v1.$BUILD_ID'
             sh 'docker rmi $JOB_NAME:v1.$BUILD_ID ${registry}:v1.$BUILD_ID' // Delete docker images from server 
           }
          }
        }
        
    } 
}   