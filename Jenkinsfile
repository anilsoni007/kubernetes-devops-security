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
      stage('Build & Push Docker Image') {
         steps {
           withDockerRegistry(credentialsId: 'DOCKER_CRED', url: 'https://index.docker.io/v1/') {
             sh "docker build -t asoni007/secops:${env.GIT_COMMIT} ."
             sh "docker push asoni007/secops:${env.GIT_COMMIT}"
          }
        }
      }
      stage('k8s-Deployment') {
        steps {
          sh "kubectl version --client"
        }
      }
      }
}
