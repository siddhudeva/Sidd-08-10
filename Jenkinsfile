pipeline {
    agent any
    stages {
        stage('SonarQualityCheck') {
            agent{
                docker {
                    image 'openjdk:11'
                }
            }
            steps{
                script{
                    withSonarQubeEnv(credentialsId: 'Sonar_token') {
                        sh 'chmod +x gradlew'
                        sh './gradlew sonarqube'
                    }

                }
            }
        }
    }
}