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
    //   stage('Mutation Tests - PIT') {
    //   steps {
    //     sh "mvn org.pitest:pitest-maven:mutationCoverage"
    //   }
    //   post {
    //     always {
    //       pitmutation mutationStatsFile: '**/target/pit-reports/**/mutations.xml'
    //     }
    //   }
    // }

    stage('build && SonarQube analysis') {
            steps {
                withSonarQubeEnv('sonar-qube-scanner') {
                    sh "mvn clean verify sonar:sonar -Dsonar.projectKey=secops -Dsonar.projectName='secops' -Dsonar.host.url=http://65.0.104.178:9000"
                }
            }
      // stage('k8s-Deployment') {
      //   steps {
      //      withKubeConfig(caCertificate: '', clusterName: '', contextName: '', credentialsId: 'kubeconfig', namespace: '', restrictKubeConfigAccess: false, serverUrl: '') {
      //       sh "kubectl version --client"
      //       sh "sed -i 's#replace#asoni007/secops:${env.GIT_COMMIT}#g' k8s_deployment_service.yaml"
      //       sh "cat k8s_deployment_service.yaml"
      //       sh "kubectl apply -f k8s_deployment_service.yaml"
      //     }
      //   }
      // }
    }
  }
}
