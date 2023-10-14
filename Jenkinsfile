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
            def nexusUrl = 'http://3.27.185.94:8081/repository/webapplication'  // Corrected Nexus URL
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
