pipeline {
    agent any

    environment {
        // Define the SSH credentials ID and remote server details
        SSH_CREDENTIALS_ID = 'tomcat-ssh-key'
        REMOTE_TOMCAT_SERVER = '44.203.174.207'
        REMOTE_USER = 'jenkins'
        REMOTE_TOMCAT_WEBAPPS = '/opt/tomcat/webapps'
        REMOTE_TOMCAT_SHUTDOWN = '/opt/tomcat/bin/shutdown.sh'
        REMOTE_TOMCAT_STARTUP = '/opt/tomcat/bin/startup.sh'
    }

    stages {
        stage('Checkout') {
            steps {
                // Checkout the source code from GitHub
                git url: 'https://github.com/ruudgong/hello-world.git'
            }
        }

        stage('Build') {
            steps {
                // Run Maven to build the project and generate a WAR file
                sh 'mvn clean package'
            }
        }

        stage('Deploy') {
            steps {
                script {
                    // Define the path to the generated WAR file
                    def warFile = 'target/*.war'

                    // Use SSH to copy the WAR file to the Tomcat webapps directory
                    sshPublisher(
                        publishers: [
                            sshPublisherDesc(
                                configName: REMOTE_TOMCAT_SERVER,
                                transfers: [
                                    sshTransfer(
                                        sourceFiles: warFile,
                                        removePrefix: 'target',  // Strip the "target" prefix from the destination path
                                        remoteDirectory: REMOTE_TOMCAT_WEBAPPS,  // Tomcat's webapps directory
                                        execCommand: '''
                                            ${REMOTE_TOMCAT_SHUTDOWN};
                                            sleep 5;
                                            ${REMOTE_TOMCAT_STARTUP}
                                        ''',  // Restart Tomcat after deploying the WAR
                                        execTimeout: 120000  // Allow up to 2 minutes for Tomcat restart
                                    )
                                ],
                                verbose: true
                            )
                        ]
                    )
                }
            }
        }
    }

    post {
        always {
            // Notify if the build succeeds or fails
            echo 'Deployment finished!'
        }
    }
}
