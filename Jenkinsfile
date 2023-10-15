pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                script {
                    def mavenHome = tool name: 'Maven', type: 'hudson.tasks.Maven$MavenInstallation'
                    sh "${mavenHome}/bin/mvn clean package"
                }
            }
        }

        stage('Deploy to Tomcat') {
            steps {
                script {
                    def tomcatUrl = 'http://3.27.185.94:8082/'  // Replace with your Tomcat URL
                    def tomcatManagerCredentialsId = 'Tomcat'  // Jenkins credentials for Tomcat Manager
                    def warFileName = 'target/onlinebookstore.war'  // The path to your WAR file

                    if (fileExists(warFileName)) {
                       sh "curl --user tomcat-user:tomcat-password --upload-file ${warFileName} ${tomcatUrl}/manager/text/deploy?path=/onlinebookstore&update=true"
                    } else {
                        error("WAR file not found")
                    }
                }
            }
        }
        stage('Copy to Tomcat Webapps') {
            steps {
                script {
                    def warFileName = 'target/onlinebookstore.war'  // The path to your WAR file
                    def tomcatWebappsDirectory = '/opt/apache-tomcat-10.1.14/webapps'

                    if (fileExists(warFileName)) {
                        sh "sudo cp ${warFileName} ${tomcatWebappsDirectory}"
                    } else {
                        error("WAR file not found for copying to Tomcat webapps")
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

def fileExists(filePath) {
    try {
        sh "test -e ${filePath}"
        return true
    } catch (Exception ignored) {
        return false
    }
}
