pipeline {
  agent any

  environment {
    TOMCAT_DEPLOY_ID='deployer'
    TOMCAT_SERVER_IP='54.210.32.21'
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
        // Uses your standard text credentials to run a raw HTTP POST upload
        withCredentials([usernamePassword(credentialsId: 'deployer', passwordVariable: 'TOMCAT_PASS', usernameVariable: 'TOMCAT_USER')]) {
            sh "curl -u ${TOMCAT_USER}:${TOMCAT_PASS} --upload-file target/resume-generator-service.war 'http://54.210.32.21:8080/manager/text/deploy?path=/resume-generator-service&update=true'"
        }
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