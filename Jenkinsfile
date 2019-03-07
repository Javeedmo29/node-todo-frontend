pipeline {
  environment{
    registry = "javeedmo29/dockerproject"
    registryCredential = 'dockerhub'
    dockerImage = ''
    containerId = sh(script: 'docker ps -aqf "name=node-app"', returnStdout:true)
  }
  agent any
  tools {nodejs "node"}
    
  stages {
    stage('Cloning Git') {
      steps {
        git 'https://github.com/Javeedmo29/node-todo-frontend'
      }
    }
        
    stage('Build') {
      steps {
        sh 'npm install'
      }
    }
     
    stage('Test') {
      steps {
         sh 'npm test'
      }
    }
    
    stage('Sonar Build') {
      steps {
        sh 'npm install -D sonarqube-scanner'
      }
    }
  
    stage('properties') {
      steps {
        sh 'sonarqube-scanner \
  -Dsonar.projectKey=project \
  -Dsonar.sources=. \
  -Dsonar.host.url=http://13.71.118.170:9000 \
  -Dsonar.login=dd9ec1ed7ff71da76a89c0425fb21764e4425526'
      }
    }
        
        
        
        
    stage('Sonar scan execution') {
            // Run the sonar scan
            steps {
                script {
                    def scannerHome = tool 'sonarqube-scanner';
                    withSonarQubeEnv {

                        sh "${scannerHome}/bin/sonar-scanner"
                    }
                }
            }
        }
        
    stage('Building Image'){
      steps{
        script{
          dockerImage = docker.build registry + ":$BUILD_NUMBER"
        }
      }
    }
    
    
    
    stage('Push Image'){
      steps{
        script{
          docker.withRegistry('',registryCredential) {
            dockerImage.push()
          }
        }
      }
    }
    stage('Cleanup'){
      when{
        not {environment ignoreCase:true, name:'containerId', value:''}
      }
      steps {
        sh 'docker stop ${containerId}'
        sh 'docker rm ${containerId}'
      }
    }
    stage('Run Container'){
      steps{
        sh 'docker run --name=node-app -d -p 3000:3000 $registry:$BUILD_NUMBER &'
      }
    }
  }
}
  
