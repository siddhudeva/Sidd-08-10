pipeline {
    agent any
    environment{
        VERSION = "${env.BUILD_ID}"
    }
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
//                    timeout(time: 15, unit: 'MINUTES') { // If analysis takes longer than indicated time, then build will be aborted
                    //    waitForQualityGate abortPipeline: true
                    //    script{
//                            def qg = waitForQualityGate() // Waiting for analysis to be completed
//                            if(qg.status != 'OK'){ // If quality gate was not met, then present error
//                                error "Pipeline aborted due to quality gate failure: ${qg.status}"
//                                slackSend channel: 'slack-jenkins', message: 'From Pipeline'
//                            }
                        //}
 //                   }
                }
            }
        }
        stage('Docker build & push to ECR') {
            steps{
                script {
                    //withCredentials([string(credentialsId: 'idofcred', variable 'variable')]) {
                    sh '''
                        aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 941746065449.dkr.ecr.us-east-1.amazonaws.com
                        docker build -t javagradle-private-${VERSION} .
                        docker tag javagradle-private-${VERSION}:latest 941746065449.dkr.ecr.us-east-1.amazonaws.com/javagradle-private-${VERSION}:latest
                        docker push 941746065449.dkr.ecr.us-east-1.amazonaws.com/javagradle-private-${VERSION}:latest
                        docker rmi javagradle-private-${VERSION}
                       '''
                    //}
                }
            }
        }
        stage('identifing the misconfiguration using datree plugin'){
            steps{
                    script{
                        dir('kubernetes/') {
                            sh 'helm datree test myapp/'
                        }
                    }
                }
            }
        }
    }
    post {
        always {
            slackSend channel: 'slack-jenkins', message: 'Docker image pushed and pipeline is succeseded'
            cleanWS()
        }
    }