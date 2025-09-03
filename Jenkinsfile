pipeline{
  agent any

  tools {
    maven "M3"
    jdk "JDK17"
  }

environment {
  DOCKERHUB_CREDENTIALS = credentials('dockerCredentials')
}
stage{
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
    docker tag harugoon603/spring-petclinic:$BUILD_NUMBER harugoon603/spring-petclinic:latest
    """
     }
    }
stage('Docker Hub Login'){
  steps {
    sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
  }
}
stage('Docker Image Push'){
  steps {
      sh 'docker push harugoon603/spring-petclinic:latest'
      }
     }

  stage('Docker Image Remove'){
  steps {
    sh 'docker rmi harugoon603/spring-petclinic:latest'
  }  
  }  
  stage('Publish Over SSH') {
  steps {
    sshPublisher(publishers: [sshPublisherDesc(configName: 'target', 
    transfers: [sshTransfer(cleanRemote: false, 
    excludes: '', 
    execCommand: '''
    dockerrm -f $(docker ps -aq)
    docker rmi $(docker images -q)
    docker run -itd -p 8080:8080 --name=spring-petclinic harugoon603/spring-petclinic:latest
    ''', 
    execTimeout: 120000, 
    flatten: false, 
    makeEmptyDirs: false,
    noDefaultExcludes: false, 
    patternSeparator: '[, ]+', 
    remoteDirectory: '', 
    remoteDirectorySDF: false, 
    removePrefix: 'target', sourceFiles: '')], 
    usePromotionTimestamp: false, 
    useWorkspaceInPromotion: false, 
    verbose: false)])
   }
  }
 }
}
