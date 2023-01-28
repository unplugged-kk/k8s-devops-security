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

      stage('SonarQube - SAST') {
        steps {
          withSonarQubeEnv('SonarQube') {
          sh "mvn sonar:sonar  -Dsonar.projectKey=numeric-application -Dsonar.host.url=http://34.69.15.12:9000"
          }
          timeout(time: 2, unit: 'MINUTES') {
          script {
            waitForQualityGate abortPipeline: true
            }
          }
        }
      }   


      stage('Vulnerability Scan - Docker') {
          steps {
            parallel(
       	      "Dependency Scan": {
       		     sh "mvn dependency-check:check"
		           },
		          "Trivy Scan":{ sh "bash trivy-docker-image-scan.sh" },
              "OPA Conftest":{ sh 'docker run --rm -v $(pwd):/project openpolicyagent/conftest test --policy opa-docker-security.rego Dockerfile'}   	
            )
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