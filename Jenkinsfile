pipeline {
  agent {
    kubernetes {
      cloud 'mars-sandbox-k8'
      namespace 'jenkins'
      yaml """
apiVersion: v1
kind: Pod
metadata:
  name: jenkins-agent
  namespace: jenkins
  labels:
    app: jenkins-agent
spec:
  serviceAccountName: jenkins-service-account
  containers:
  - name: jnlp
    image: jenkins/inbound-agent:latest
    args: ['jnlp']
  - name: kubectl
    image: bitnami/kubectl:latest  # Ensures kubectl is available
    command: ['sleep', 'infinity']
"""
    }
  }

  stages {
      // stage('Build Artifact') {
      //       steps {
      //         sh "mvn clean package"
      //         archive 'target/*.jar' //so that they can be downloaded later
      //       }
      //   } 
      // stage('Unit Test') {
      //    steps {
      //     sh "mvn test"
      //    }
      //    post {
      //      always {
      //         junit 'target/surefire-reports/*.xml'
      //         jacoco execPattern: 'target/jacoco.exec'
      //      }
      //    }
      // }
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
          sh "sed -i 's#replace#asoni007/secops:${env.GIT_COMMIT}#g' k8s_deployment_service.yaml"
          sh "cat k8s_deployment_service.yaml" 
          sh "kubectl apply -f k8s_deployment_service.yaml --validate=false"
        }
      }
      }
}
