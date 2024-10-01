pipeline {
    agent any

    tools {
        maven 'M3'  // Replace 'Maven3' with the name you set in Jenkins for Maven
    }

    environment {
        // Define the SSH credentials ID and remote server details
        SSH_CREDENTIALS_ID = 'tomcat-ssh-key'
        REMOTE_TOMCAT_SERVER = '44.203.174.207'  // Replace with your actual server IP or hostname
        REMOTE_TOMCAT_WEBAPPS = '/opt/tomcat/webapps'  // Path to the webapps directory in Tomcat
        REMOTE_TOMCAT_SHUTDOWN = '/opt/tomcat/bin/shutdown.sh'  // Shutdown script for Tomcat
        REMOTE_TOMCAT_STARTUP = '/opt/tomcat/bin/startup.sh'    // Startup script for Tomcat
    }

    stages {
        stage('Checkout') {
            steps {
                // Checkout the source code from GitHub
                git url: 'https://github.com/ruudgong/hello-world.git', branch: 'master'
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
                    // Deploy the WAR file to the remote Tomcat server via SSH
                    sshPublisher(
                        publishers: [
                            sshPublisherDesc(
                                configName: 'fb25259c772c.mylabserver.com',  // Make sure this matches the SSH server config name
                                transfers: [
                                    sshTransfer(
                                        sourceFiles: '**/target/*.war',   // WAR file location
                                        removePrefix: 'target',           // Strip the "target" prefix in the destination
                                        remoteDirectory: '/opt/tomcat/webapps/',  // Path to Tomcat's webapps directory
                                        execCommand: """
                                            ${REMOTE_TOMCAT_SHUTDOWN};
                                            sleep 5;
                                            ${REMOTE_TOMCAT_STARTUP};
                                        """,  // Restart Tomcat after deploying the WAR
                                        execTimeout: 120000  // Timeout for the commands to execute
                                    )
                                ],
                                verbose: true  // Enable detailed logs for debugging
                            )
                        ]
                    )
                }
            }
        }
    }

    post {
        always {
            echo 'Deployment finished!'
        }
    }
}
