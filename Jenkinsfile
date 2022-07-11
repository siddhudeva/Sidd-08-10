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
                    timeout(time: 5, unit: 'MINUTES') { // If analysis takes longer than indicated time, then build will be aborted
                    //    waitForQualityGate abortPipeline: true
                    //    script{
                            def qg = waitForQualityGate() // Waiting for analysis to be completed
                            if(qg.status != 'OK'){ // If quality gate was not met, then present error
                                error "Pipeline aborted due to quality gate failure: ${qg.status}"
                            }
                        //}
                    }
                }
            }
        }
    }
}