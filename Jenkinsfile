pipeline {
    agent any

    tools {
        // Name should match the one you configured in the Global Tool Configuration
        maven 'Maven'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean install'
        }
    }

       stage('Deploy to Tomcat') {
            steps {
                script {
                    def tomcatUrl = 'http://3.27.185.94:8082/'  // Replace with your Tomcat URL
                    def tomcatManagerCredentialsId = 'Tomcat'  // Jenkins credentials for Tomcat Manager

                    sh "curl --user tomcat-user:tomcat-password --upload-file target/onlinebookstore.war ${tomcatUrl}/manager/text/deploy?path=/onlinebookstore&update=true"
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment successful'
        }
    }
}
