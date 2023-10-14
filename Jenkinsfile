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
                sh 'mvn clean install'
            }
        }

        stage('Publish to Nexus') {
            steps {
                script {
                    def nexusUrl = 'http://13.210.249.199:8081/'  // Replace with your Nexus URL
                    def nexusRepository = 'webapplication'  // Replace with your Nexus repository
                    def nexusCredentialsId = 'nexus'  // Jenkins credentials for Nexus

                    def artifactVersion = sh(script: 'mvn help:evaluate -Dexpression=project.version -q -DforceStdout', returnStdout: true).trim()
                    def artifactFile = sh(script: 'find target/ -name "*.war" -type f', returnStdout: true).trim()

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
                }
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
