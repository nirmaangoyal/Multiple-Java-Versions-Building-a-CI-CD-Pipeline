pipeline {
    agent any

    tools {
        maven 'M3'
    }

    environment {
        PATH = "${tool 'M3'}/bin:${env.PATH}"
    }

    stages {
        stage('Setup JDK') {
            matrix {
                axes {
                    axis {
                        name 'JDK_VERSION'
                        values 'Java 14', 'Java 17'
                    }
                }
                stages {
                    stage('Checkout') {
                        steps {
                            checkout scm
                        }
                    }
                    stage('Compile') {
                        steps {
                            script {
                                def javaHome = tool name: "${JDK_VERSION}", type: 'jdk'
                                env.JAVA_HOME = javaHome
                                env.PATH = "${javaHome}/bin:${env.PATH}"
                            }
                            sh 'mvn compile'
                        }
                    }
                    stage('Test') {
                        steps {
                            sh 'mvn test'
                            junit allowEmptyResults: true,
                                 testResults: '**/target/surefire-reports/*.xml'
                        }
                    }
                    stage('Package') {
                        steps {
                            sh 'mvn package'
                            archiveArtifacts artifacts: '**/*.jar', allowEmptyArchive: true
                        }
                    }
                    stage('Deploy') {
                        steps {
                            sh 'mvn deploy'
                        }
                    }
                }
            }
        }
    }

    post {
        always {
            cleanWs()
        }
        success {
            echo 'Project built successfully!'
        }
        failure {
            echo 'Failure in Development'
            script {
                // Add any specific actions you want to perform on failure, like notifications
            }
        }
    }
}
