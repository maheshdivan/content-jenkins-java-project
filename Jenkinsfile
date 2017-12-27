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
        sh "rm -rf /var/www/html/rectangles/all/${BRANCH_NAME}"
        sh "mkdir /var/www/html/rectangles/all/${BRANCH_NAME}"
        sh 'cp dist/rectangle__${MAJOR_VERSION}.${BUILD_NUMBER}.jar /var/www/html/rectangles/all/${BRANCH_NAME}/'
      }
    }
    stage('Running on CentOs'){
      agent{
      label 'CentOs'
      }
      steps {
        sh "wget http://mahesh-19791.mylabserver.com/rectangles/all/${BRANCH_NAME}/rectangle__${MAJOR_VERSION}.${BUILD_NUMBER}.jar"
        sh "java -jar rectangle__${MAJOR_VERSION}.${BUILD_NUMBER}.jar 3 4"
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
          sh "cp /var/www/html/rectangles/all/${BRANCH_NAME}/rectangle__${MAJOR_VERSION}.${BUILD_NUMBER}.jar /var/www/html/rectangles/green/rectangle_${BUILD_NUMBER}.jar"           }
                
          }   
    
    stage ('Promote development to Master') {
      
      agent{
      label 'apache'
      }
    
      when {
      branch 'development'
      }
      steps {
      echo "Staching all local changes"
        sh 'git stash'
        echo "Checkout development"
        sh 'git checkout development'
        echo "checking out master"
        sh 'git pull origin'
        sh ' git checkout master'
        sh 'git pull'
        
        echo "merging development into master"
        sh 'git merge development master'
        echo "pushing to origin master"
        sh 'git push origin master'
        echo 'Tagging the release'
        sh 'git tag rectangle-${MAJOR_VERSION}.${BUILD_NUMBER}'
        sh 'git push origin rectangle-${MAJOR_VERSION}.${BUILD_NUMBER}'
      }
    }
    post {
      failure{
      emailext(
      subject: "Failure",
      body: "Build failed",
       to: "mahesh490@gmail.com" 
      )
      }
    }
  }
 
  
}  
    
