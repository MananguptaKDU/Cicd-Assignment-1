pipeline {
    agent any

    environment {
        AWS_REGION = 'ap-northeast-1'
        EB_APPLICATION_NAME = 'cicd-pipeline'
        EB_ENVIRONMENT_NAME = 'dev'
        S3_BUCKET = 'cicd-pipeline-artifacts'

        JAVA_HOME = tool 'JDK17'
        MAVEN_HOME = tool 'Maven17'
        // ⚠ Don't set PATH here with JAVA_HOME and MAVEN_HOME
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
