pipeline {
    agent any

    environment {
        AWS_REGION = 'ap-northeast-1'
        EB_APPLICATION_NAME = 'cicd-pipeline'
        EB_ENVIRONMENT_NAME = 'dev'
        S3_BUCKET = 'cicd-pipeline-artifacts'

        JAVA_HOME = tool 'JDK17'
        MAVEN_HOME = tool 'Maven17'
        PATH = "${JAVA_HOME}/bin:${MAVEN_HOME}/bin:${env.PATH}"
    }

    stages {
        stage('Init Check') {
            steps {
                echo '✅ Jenkins pipeline is working!'
                echo "📦 Application: ${EB_APPLICATION_NAME}"
                echo "🌍 Region: ${AWS_REGION}"
                echo "📁 S3 Bucket: ${S3_BUCKET}"
                echo "📦 Java: ${JAVA_HOME}"
                echo "🔧 Maven: ${MAVEN_HOME}"
                sh 'mvn -version'
                sh 'java -version'
            }
        }
    }

    post {
        always {
            cleanWs()
        }
        success {
            echo '✅ Basic pipeline completed successfully!'
        }
        failure {
            echo '❌ Pipeline failed!'
        }
    }
}
