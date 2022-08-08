pipeline {
    agent any

    environment {

        AWS_ACCESS_KEY_ID     = credentials('Mastoura-aws-secret-key-id')
        AWS_SECRET_ACCESS_KEY = credentials('Mastoura-aws-secret-access-key')

        AWS_S3_BUCKET = "mastoura-belt2d2-artifacts-123456"
        ARTIFACT_NAME = "Hello-world.jar"
        AWS_EB_APP_NAME = "mastoura-exam"
        AWS_EB_APP_VERSION = "${BUILD_ID}"
        AWS_EB_ENVIRONMENT = "Mastouraexam-env"

        SONAR_IP = "52.23.193.18"
        SONAR_TOKEN = "sqp_51d0bf5e472bc9545fa7d591f8dd8c87231f3d76"

    }

    stages {
        stage('Validate') {
            steps {
                
                sh "mvn validate"

                sh "mvn clean"

            }
        }

         stage('Build') {
            steps {
                
                sh "mvn compile"

            }
        }

        stage('Test') {
            steps {
                
                sh "mvn test"

            }

            post {
                always {
                    junit '**/target/surefire-reports/TEST-*.xml'
                    
                }
            }
        }

        stage('Quality Scan'){
            steps {
                sh '''
                mvn clean verify sonar:sonar \
                    -Dsonar.projectKey=convention---online-Mastoura-B2D2 \
                    -Dsonar.host.url=http://$SONAR_IP \
                    -Dsonar.login=$SONAR_TOKEN
                '''
            }
        }

        stage('Package') {
            steps {
                
                sh "mvn package"

            }

            post {
                success {
                    archiveArtifacts artifacts: '**/target/**.jar', followSymlinks: false

                   
                }
            }
        }

        stage('Publish artefacts to S3 Bucket') {
            steps {

                sh "aws configure set region us-east-1"

                sh "aws s3 cp ./target/**.jar s3://$AWS_S3_BUCKET/$ARTIFACT_NAME"
                
            }
        }
        

        
        
    }
}