pipeline {
    agent any

    environment {
        AWS_REGION = 'ap-northeast-1'
        EB_APPLICATION_NAME = 'spring-boot-app'
        EB_ENVIRONMENT_NAME = 'dev-eb-env'
        S3_BUCKET = 'dev-eb-artifacts'

        JAVA_HOME = tool 'JDK17'
        MAVEN_HOME = tool 'Maven 3'
    }

    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/MananguptaKDU/Cicd-Assignment-1.git', branch: 'main'
            }
        }

        stage('Build JAR') {
            steps {
                withEnv(["PATH=${env.JAVA_HOME}/bin:${env.MAVEN_HOME}/bin:${env.PATH}"]) {
                    sh 'mvn clean package'
                    sh 'cp target/*.jar target/app.jar'  // Rename to fixed name
                }
            }
            post {
                success {
                    archiveArtifacts artifacts: 'target/app.jar', fingerprint: true
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    withEnv(["PATH=${env.JAVA_HOME}/bin:${env.MAVEN_HOME}/bin:${env.PATH}"]) {
                        sh 'mvn sonar:sonar'
                    }
                }
            }
        }

        stage('Push to S3') {
            steps {
                withEnv(["PATH=${env.JAVA_HOME}/bin:${env.MAVEN_HOME}/bin:${env.PATH}"]) {
                    sh """
                        aws s3 cp target/app.jar s3://${S3_BUCKET}/artifacts/app.jar \
                        --region ${AWS_REGION}
                    """
                }
            }
        }

        stage('Deploy to Elastic Beanstalk') {
            steps {
                withEnv(["PATH=${env.JAVA_HOME}/bin:${env.MAVEN_HOME}/bin:${env.PATH}"]) {
                    script {
                        def versionLabel = "v-${BUILD_NUMBER}"
                        sh """
                            aws elasticbeanstalk create-application-version \
                                --application-name ${EB_APPLICATION_NAME} \
                                --version-label ${versionLabel} \
                                --source-bundle S3Bucket="${S3_BUCKET}",S3Key="artifacts/app.jar" \
                                --region ${AWS_REGION}

                            aws elasticbeanstalk update-environment \
                                --environment-name ${EB_ENVIRONMENT_NAME} \
                                --version-label ${versionLabel} \
                                --region ${AWS_REGION}
                        """
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
            echo '✅ Pipeline completed successfully!'
        }
        failure {
            echo '❌ Pipeline failed!!'
        }
    }
}
