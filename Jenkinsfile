pipeline {
  agent any

  environment {
    PROJECT_KEY = 'numeric-application'
    SONAR_TOKEN = credentials('SONAR_TOKEN')
  }

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
                        alwaysLinkToLastBuild: true,
                        allowMissing: false
                    ])
                }
            }
        }

        stage('SonarQube - SAST') {
            steps {
                sh 'mvn clean package -DskipTests=true'
                archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
                sh """
                    mvn clean verify org.sonarsource.scanner.maven:sonar-maven-plugin:sonar \
                      -Dsonar.projectKey=${PROJECT_KEY} \
                      -Dsonar.host.url=http://devsecops-demo.eastus.cloudapp.azure.com:9000 \
                      -Dsonar.login=${SONAR_TOKEN}
                """
            }
        }
    }
}