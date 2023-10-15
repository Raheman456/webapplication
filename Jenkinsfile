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
                    def warFileName = 'onlinebookstore.war'  // Name of your WAR file

                    def warFile = sh(script: "find target/ -name '${warFileName}' -type f", returnStdout: true).trim()

                    if (warFile) {
                        sh "curl --user tomcat-user:tomcat-password --upload-file ${warFile} ${tomcatUrl}/manager/text/deploy?path=/onlinebookstore&update=true"
                    } else {
                        error("WAR file not found")
                    }
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment to Tomcat successful'
        }
        failure {
            echo 'Deployment to Tomcat failed'
        }
    }
}
