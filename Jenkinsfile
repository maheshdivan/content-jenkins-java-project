pipeline {
  agent none
  
  options {
    buildDiscarder(logRotator(numToKeepStr: '2', artifactNumToKeepStr: '1'))
  
  }

  environment {
    MAJOR_VERSION = 1
  }

  stages {
    stage('unit tests'){
      agent{
      label 'apache'
      }
      steps {
      sh 'ant -f test.xml -v'
      junit 'reports/result.xml'  
      }
    }
    stage('build') {
      agent{
      label 'apache'
      }
      steps {
        sh 'ant -f build.xml -v'
      }
       post{
         always{
            archiveArtifacts artifacts: 'dist/*.jar',fingerprint:true 
    
             }
         }
    }
    stage('deploy'){
      agent{
      label 'apache'
      }
      steps {
        sh 'cp dist/rectangle_${BUILD_NUMBER}.jar /var/www/html/rectangles/all/'
      }
    }
    stage('Running on CentOs'){
      agent{
      label 'CentOs'
      }
      steps {
        sh "wget http://mahesh-19791.mylabserver.com/rectangles/all/rectangle_${BUILD_NUMBER}.jar"
        sh "java -jar rectangle_${BUILD_NUMBER}.jar 3 4"
      }
      
    }
    
    stage('promote to Green') {
      
      agent{
      label 'apache'
      }
      
      when{
      branch 'development'
      }
          steps {
          sh "cp /var/www/html/rectangles/all/rectangle_${BUILD_NUMBER}.jar /var/www/html/rectangles/green/rectangle_${BUILD_NUMBER}.jar"           }
                
          }   
  }
 
}  
    
