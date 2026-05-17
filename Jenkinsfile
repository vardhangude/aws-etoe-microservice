pipeline {
    agent any

    tools {
        jdk 'JDK-21'      // Must match Jenkins Tools config name
        maven 'Maven-3'   // Must match Jenkins Tools config name
        git 'Git'
    }

    environment {
        APP_NAME    = 'my-java-app'
        GIT_REPO    = 'https://github.com/vardhangude/aws-etoe-microservice.git'
        DEPLOY_PATH = '/opt/apps'
    }

    stages {

        stage('Clone') {
            steps {
                git branch: 'main',
                    credentialsId: 'github-creds',
                    url: "${GIT_REPO}"
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package -DskipTests'
                // Output: target/<app>.jar or .war
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test'
            }
            post {
                always {
                    junit allowEmptyResults: true,
                          testResults: 'target/surefire-reports/*.xml'
                }
            }
        }

        stage('Package') {
            steps {
                archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
            }
        }

        stage('Deploy') {
            when { branch 'main' }
            steps {
                sh """
                    mkdir -p ${DEPLOY_PATH}
                    cp target/*.jar ${DEPLOY_PATH}/${APP_NAME}.jar
                    echo 'Deployed!'
                """
            }
        }
    }

    post {
        success { echo 'Pipeline completed successfully!' }
        failure { echo 'Pipeline FAILED — check logs above.' }
        always  { cleanWs() }
    }
}
