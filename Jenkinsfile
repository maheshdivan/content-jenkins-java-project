pipeline {
  agent any
  
  options {
    buildDiscarder(logRotator(numToKeepStr: '2', artifactNumToKeepStr: '1'))
  
  }

  environment {
    MAJOR_VERSION = 1
  }

  stages {
    stage('unit tests'){
      steps {
      sh 'ant -f test.xml -v'
      junit 'results/results.xml'  
      }
    }
    stage('build') {
      steps {
        sh 'ant -f build.xml -v'
      }
    }
  }
  post{
    always{
      archiveArtifacts artifacts: 'dist/*.jar',fingerprint:true 
    
    }
  
  }
}  
    
