pipeline {
    agent any {
    tools {
        maven 'Maven-3'
        jdk 'JDK-21'
    }
        environment {
            APP_NAME = 'my_java_app'
            GIT_REPO = 'https://github.com/vardhangude/aws-etoe-microservice.git'
            DEPLOY_PATH = '/opt/apps'
        }
    }

    stages{
        stage ('Debug') {
            steps{
                sh 'echo $JAVA_HOME'
                sh 'java -version'
                sh 'mvn -version'
            }
        }

        stage ('Clone') {
            steps {
                git branch: '1.0'
                url: "${GIT_REPO}"
            }
        }

        stage ('Build) {
               steps {
                 sh 'mvn clean package -DskipTests'
               }
        }

        stage ('Test') {
            steps {
                sh 'mvn test'
            }
            post {
                always {
                    junit allowEmptyResults: true, testResults: 'target/surefire-reports/*.xml'     
                }
            }
        }

        stage ('Package') {
            steps {
                archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
            }
        }

        stage('Deploy') {
            when { branch '1.0' }
            steps {
                sh """
                    mkdir -p ${DEPLOY_PATH}
                    cp target/*.jar ${DEPLOY_PATH}/{APP_NAME}.jar
                    echo 'DEPLOYED!'
                """
            }
        }

        post {
            success { echo 'Pipeline completed Succesfully' }
            failure { echo 'Pipeline Failed - check logs above' }
            always { cleansWs() }
        }
    }
}
