pipeline {
  agent any

  stages {
      stage('Build Artifact') {
            steps {
              sh "mvn clean package -DskipTests=true"
              archiveArtifacts artifacts: 'target/*.jar' 
            }
        }  


      stage('Unit Tests - Junit & JaCoCo') {
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


       stage('Mutation Tests - PIT') {
          steps {
            sh "mvn org.pitest:pitest-maven:mutationCoverage"
          }
          post { 
              always { 
                pitmutation mutationStatsFile: '**/target/pit-reports/**/mutations.xml'
              }
          }

        }  

       stage('SonarQube- SAST') {
          steps {
            sh "mvn clean verify sonar:sonar -Dsonar.projectKey=numeric-application -Dsonar.host.url=http://34.69.15.12:9000 -Dsonar.login=sqp_a4ba17d2210b0ecb0e430695fee8838f7c4a8dc5"
          }
        }    

      stage('Docker Build and Push') {
          steps {
            withDockerRegistry([credentialsId: "docker-hub", url: ""]) {
            sh 'printenv'
            sh 'sudo docker build -t unpluggedkk/numeric-app:""$GIT_COMMIT"" .'
            sh 'docker push unpluggedkk/numeric-app:""$GIT_COMMIT""'
            }
          }
        }
        
      stage('Kubernetes Deployment - DEV') {
          steps {
            withKubeConfig([credentialsId: 'kubeconfig']) {
            sh "sed -i 's#replace#unpluggedkk/numeric-app:${GIT_COMMIT}#g' k8s_deployment_service.yaml"
            sh "kubectl  apply -f k8s_deployment_service.yaml"
            }
          }
        }
      

     

    } 
}