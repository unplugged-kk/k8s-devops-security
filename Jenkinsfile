pipeline {
  agent any

  stages {
      stage('Build Artifact') {
            steps {
              sh "mvn clean package -DskipTests=true"
              archiveArtifacts artifacts: 'target/*.jar' 
            }
        }  
      stage('Unit Tests') {
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
    } 
}