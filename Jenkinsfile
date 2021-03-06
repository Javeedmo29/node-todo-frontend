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
          sh"sonar-scanner \
  -Dsonar.projectKey=project \
  -Dsonar.sources=. \
  -Dsonar.host.url=http://13.71.82.249:9000 \
  -Dsonar.login=324491db0ddb57285f4b2e2a7a96605cb31531b1"
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
  
