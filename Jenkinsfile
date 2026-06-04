pipeline {
  agent any

  environment {
    TOMCAT_DEPLOY_ID='deployer'
    TOMCAT_SERVER_IP='107.22.139.149'
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
        deploy artifacts: 'target/resume-generator-service.war',
               contextPath: 'resume-generator',
               war: 'target/resume-generator-service.war',
               containerId: 'tomcat9x', // Note: plugin uses tomcat9x profile even for newer versions sometimes
               url: 'http://107.22.139.149:8080/',
               credentialsId: 'tomcat-manager-credentials'
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