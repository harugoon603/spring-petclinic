pipeline{
  agent any

  tools {
    maven "M3"
    jdk "JDK17"
  }

stages{
  stage('Git Clone'){
    steps {
      git url: 'https://github.com/harugoon603/spring-petclinic.git', branch:'main' 
    } 
  }
  stage('Maven Build'){
    steps {
      sh 'mvn -Dmaven.test.failure.ignore=true clean package'
    }
   }
stage('Docker Image Create'){
  steps{
    sh"""
    docker build -t harugoon603/spring-petclinic:$BUILD_NUMBER .
    """
     }
    }
   }
  }
