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
        stege('building docker image') {
            steps {
                sh 'echo build'
            }
        }
        stage('Pushing to ECR') {
            steps{
                script {
                    sh 'aws ecr-public get-login-password --region us-east-1 | docker login --username AWS --password-stdin public.ecr.aws/x4k0v2o9'
                    sh 'docker build -t java-project .'
                    sh 'docker tag java-project:latest public.ecr.aws/x4k0v2o9/java-project:latest'
                    sh 'docker push public.ecr.aws/x4k0v2o9/java-project:latest'
                }
            }
        }
    }
}