pipeline {
    agent any

    environment {
        AWS_REGION = 'ap-northeast-1'
        EB_APPLICATION_NAME = 'cicd-pipeline'
        EB_ENVIRONMENT_NAME = 'dev'
        S3_BUCKET = 'dev-eb-artifacts'  // ✅ Updated bucket name

        JAVA_HOME = tool 'JDK17'
        MAVEN_HOME = tool 'Maven 3'
    }

    stages {
        stage('Init Check') {
            steps {
                withEnv(["PATH=${env.JAVA_HOME}/bin:${env.MAVEN_HOME}/bin:${env.PATH}"]) {
                    echo '✅ Jenkins pipeline is working!'
                    echo "📦 Application: ${env.EB_APPLICATION_NAME}"
                    echo "🌍 Region: ${env.AWS_REGION}"
                    echo "📁 S3 Bucket: ${env.S3_BUCKET}"
                    echo "📦 Java: ${env.JAVA_HOME}"
                    echo "🔧 Maven: ${env.MAVEN_HOME}"
                    sh 'mvn -version'
                    sh 'java -version'
                }
            }
        }

        stage('Checkout Code') {
            steps {
                git url: 'https://github.com/MananguptaKDU/Cicd-Assignment-1.git', branch: 'main'
            }
        }

        stage('Build') {
            steps {
                withEnv(["PATH=${env.JAVA_HOME}/bin:${env.MAVEN_HOME}/bin:${env.PATH}"]) {
                    sh 'mvn clean package -DskipTests'
                }
            }
            post {
                success {
                    archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
                }
            }
        }

        stage('Push to S3') {
            steps {
                withEnv(["PATH=${env.JAVA_HOME}/bin:${env.MAVEN_HOME}/bin:${env.PATH}"]) {
                    sh """
                        aws s3 cp target/*.jar s3://${S3_BUCKET}/artifacts/${BUILD_NUMBER}.jar --region ${AWS_REGION}
                    """
                }
            }
        }
    }

    post {
        always {
            cleanWs()
        }
        success {
            echo '✅ Pipeline completed successfully up to S3 upload!'
        }
        failure {
            echo '❌ Pipeline failed!'
        }
    }
}
