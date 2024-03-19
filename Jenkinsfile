pipeline {
  
    agent { 
        any
    }    
    environment {
        registry = "183465517923.dkr.ecr.us-east-1.amazonaws.com/vprofileapp"
    }
    
    stages {
        stage('Cloning Git') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: '', url: 'https://github.com/shahu043/zon-coffee.git']]])     
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