pipeline {
  agent {
    label "java"
  }
  stages {
    stage('Build') {
      steps{
        sh 'mvn clean install'
      }
    }
    stage('jacoco') {
      steps{
        jacoco()
      }
    }

    stage('SonarQube analysis') {
      steps{
        script {
          def scannerHome = tool 'sonarqubescanner';
          withSonarQubeEnv('sonarqube') {
            sh """
              ${scannerHome}/bin/sonar-scanner \
              -Dsonar.projectKey=javawebapp \
              -Dsonar.projectName=javawebapp \
              -Dsonar.projectVersion=1.0 \
              -Dsonar.java.binaries='target/classes'
            """
          }
        }
      }
    }

   /*
    stage("Sonar Quality Gate Check") {
            steps {
                timeout(time: 1, unit: 'HOURS') {
                    script {
                        def qualityGate = waitForQualityGate()
                        if (qualityGate.status != 'OK') {
                            error "Pipeline aborted due to quality gate failure: ${qualityGate.status}"
                        }
                    }
                } // End of timeout
            }
    }
*/
  
    stage('Upload to Nexus') {
      steps{
        nexusArtifactUploader artifacts: [[artifactId: 'SimpleWebApplication', classifier: '', file: 'target/SimpleWebApplication.war', type: 'war']], credentialsId: 'nexus-jenkins', groupId: 'com.maven', nexusUrl: '172.31.37.76:8081', nexusVersion: 'nexus3', protocol: 'http', repository: 'maven-releases', version: '1.0.0'
      }
    }
  
    stage('Deploy to Tomcat') {
      steps{
        sh 'echo "Here we deploy the build to tomcat"'
      }
    }
  }
}
