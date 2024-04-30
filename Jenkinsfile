pipeline {
   agent any

   tools {
      maven "M3"
      jdk 'Java 14'
      jdk 'Java 17'
   }

   stages {
      stage('Build with Multiple JDK Versions') {
         matrix {
            axes {
               axis {
                  name 'JDK_VERSION'
                  values 'Java 14', 'Java 17'
               }
            }
            stages {
               stage('Compile') {
                  steps {
                     checkout scmGit(branches: [
                        [name: '*/main']
                     ], extensions: [], userRemoteConfigs: [
                        [url: 'https://github.com/nirmaangoyal/Multiple-Java-Versions-Building-a-CI-CD-Pipeline.git']
                     ])
                     sh "mvn compile"
                  }
               }

               stage('Test') {
                  steps {
                     sh "mvn test"
                        junit allowEmptyResults: true,
                        testResults: '**/target/surefire-reports/TEST-{JDK_VERSION}.xml'
                  }
               }

               stage('Package') {
                  steps {
                     sh "mvn package"
                     archiveArtifacts artifacts: '**/*.jar',
                        allowEmptyArchive: true
                  }
               }

               stage('Deploy') {
                  steps {
                     sh "mvn deploy"
                  }
               }
            }
         }
      }
   }
    post {
        always {
            sh 'rmdir /S /Q *' // Clean up workspace
        }
        success {
            // Actions to perform if the pipeline is successful
            echo 'Project built successfully!'

        }
        failure {
            // Actions to perform if the pipeline fails
            echo 'Failure in Development'
        }
    }
}
