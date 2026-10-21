pipeline {
  agent any

  stages {
      stage('Build Artifact - Maven') {
            steps {
              sh "mvn clean package -DskipTests=true"
              archive 'target/*.jar'
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

        stage('Mutation Tests - PIT') {
            steps {
              sh 'mvn org.pitest:pitest-maven:mutationCoverage'
            }
            post {
                always {
                    publishHTML([
                        reportDir: 'target/pit-reports',
                        reportFiles: 'index.html',
                        reportName: 'PIT Mutation Report',
                        keepAll: true,
                        alwaysLinkToLastBuild: true
                    ])
                }
            }
        }
    }
}