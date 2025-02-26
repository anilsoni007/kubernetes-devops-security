pipeline {
  agent any

  stages {
      stage('Build Artifact') {
            steps {
              sh "mvn clean package"
              archive 'target/*.jar' //so that they can be downloaded later
            }
        } 
      stage('Unit Test') {
         steps {
          sh "mvn test"
         }
         post {
           always {
              junit 'target/surefire-reports/*.xml'
              jacoco execPattern: 'target/jacoco.exec'
           }
         }
      }
      stage('Build Docker Image') {
        steps {
          withCredentials([usernamePassword(credentialsId: 'DOCKER_CRED')]) {
            sh 'docker build -t asoni007/secops:${GIT_COMMIT} .'
            sh 'docker push asoni007/secops:${GIT_COMMIT}'
          }
        }
      }
    }
}