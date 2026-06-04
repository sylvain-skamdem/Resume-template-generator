pipeline {
  agent any

  environment {
    TOMCAT_DEPLOY_ID='deployer'
    TOMCAT_SERVER_IP='34.207.136.141'
    TOMCAT_SERVER_PORT='8080'
    WAR_FILE_BUILT='target/r*.war'
  }

  tools {
        // Must match the name defined in Jenkins global tool configuration
        maven 'Maven' 
        jdk 'Java25'
        git 'git'
    }

  stages{
    stage('Checkout') {
          steps {
              // Pulls source code from your version control
              checkout scm
          }
    }
    stage('Maven Clean') {
      steps {
        sh 'mvn clean'
      }
    }
    stage('Maven Install') {
      steps {
        sh 'mvn -B install'
      }
    }
    stage('Maven Test') {
      steps {
        sh 'mvn test'
      }
    }
    stage('Maven Package WAR file') {
      steps {
        sh 'mvn package'
      }
    }
    stage('Deploy to Tomcat 11') {
      steps {
          // Deploy step provided by the Deploy to Container Plugin
          deploy artifacts: '${env.WAR_FILE_BUILT}', 
                contextPath: '', 
                war: null, 
                containers: [
                    tomcat9x(
                        url: 'http://${env.TOMCAT_SERVER_IP}:${env.TOMCAT_SERVER_PORT}',
                        credentialsId: "${env.TOMCAT_DEPLOY_ID}",
                    )
                ]
      }
    }
  }
  post {
      always {
          // Clean up workspace to prevent disk space issues
          cleanWs()
      }
      failure {
          echo "Pipeline failed. Check build logs or server connectivity."
      }
  }
}