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
        }  

    } 
}