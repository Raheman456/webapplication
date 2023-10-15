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
        stage('Publish to Nexus') {
    steps {
        script {
            def nexusUrl = 'http://3.27.185.94:8081'  // Corrected Nexus URL
            def nexusRepository = 'webapplication'  // Replace with your Nexus repository
            def nexusCredentialsId = 'nexus'  // Jenkins credentials for Nexus

            def artifactVersion
            def artifactFile

            // Try to retrieve the version and artifact file
            try {
                artifactVersion = sh(script: 'mvn help:evaluate -Dexpression=project.version -q -DforceStdout', returnStdout: true).trim()
                artifactFile = sh(script: 'find target/ -name "*.war" -type f', returnStdout: true).trim()
            } catch (Exception e) {
                error("Failed to retrieve version and artifact file: ${e.message}")
            }

            // Check if version and artifact file were successfully obtained
            if (artifactVersion && artifactFile) {
                nexusArtifactUploader(
                    nexusVersion: 'nexus3',
                    protocol: 'http',
                    nexusUrl: nexusUrl,
                    groupId: 'onlinebookstore',
                    version: "${artifactVersion}",
                    repository: nexusRepository,
                    credentialsId: nexusCredentialsId,
                    artifacts: [
                        [artifactId: 'onlinebookstore', classifier: '', file: artifactFile]
                    ]
                )
            } else {
                error("Version or artifact file not found")
            }
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
                        sh "cp ${warFileName} ${tomcatWebappsDirectory}"
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
