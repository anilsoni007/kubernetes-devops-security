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

      // stage('build & SonarQube analysis') {
      //       steps {
      //           withSonarQubeEnv('sonar-qube-scanner') {
      //               sh "mvn clean verify sonar:sonar -Dsonar.projectKey=secops -Dsonar.projectName='secops' -Dsonar.host.url=http://43.205.135.155:9000"
      //           //     timeout(time: 1, unit: 'HOURS') {
      //           //     waitForQualityGate abortPipeline: true
      //           // }
      //           }
      //       }
      //     }

      stage('owasp-dependencyCheck') {
        steps {
          sh "sudo mvn dependency-check:check"
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

    
        }
      }
