pipeline {
    agent any

    tools {
        maven 'maven'
    }

    environment {
        WAR_NAME = 'forest-1.0-SNAPSHOT.war'
        NEXUS_URL = 'http://65.0.18.51:8081/repository/maven-releases/'
        NEXUS_CREDENTIALS_ID = 'nexus-cred'
        MAVEN_SETTINGS = 'settings.xml' // Path to your custom settings.xml for Maven
        GIT_CREDENTIALS_ID = 'git-cred' // Define Git credentials ID here
        SSH_CREDENTIALS_ID = 'ssh-cred-tomcat' // Jenkins credentials for tomcat user
        TOMCAT_SERVER = '172.31.2.124' // Private IP of your Tomcat server
        TOMCAT_WEBAPPS_PATH = '/root/tomcat/webapps' // Path to webapps
    }

    stages {
        stage('Checkout Code from GitHub') {
            steps {
                git credentialsId: env.GIT_CREDENTIALS_ID, url: 'https://github.com/tohidhanfi20/Nexus-project.git', branch: 'main'
            }
        }

        stage('Build WAR File') {
            steps {
                script {
                    // Run Maven to create the WAR file
                    sh """
                        mvn -s ${MAVEN_SETTINGS} clean package
                    """
                }
            }
        }

        stage('Deploy WAR to Nexus') {
            steps {
                withCredentials([usernamePassword(credentialsId: NEXUS_CREDENTIALS_ID, usernameVariable: 'NEXUS_USER', passwordVariable: 'NEXUS_PASS')]) {
                    script {
                        // Deploy the WAR file to Nexus
                        sh """
                            mvn -s ${MAVEN_SETTINGS} deploy
                        """
                    }
                }
            }
        }

        stage('Transfer WAR to Tomcat Server') {
            steps {
                withCredentials([sshUserPrivateKey(credentialsId: SSH_CREDENTIALS_ID, keyFileVariable: 'SSH_KEY', usernameVariable: 'SSH_USER')]) {
                    script {
                        // Transfer WAR file to Tomcat server's webapps directory
                        sh """
                            scp -o StrictHostKeyChecking=no -i \$SSH_KEY target/${WAR_NAME} ${SSH_USER}@${TOMCAT_SERVER}:${TOMCAT_WEBAPPS_PATH}/
                        """
                    }
                }
            }
        }

        stage('Restart Tomcat') {
            steps {
                withCredentials([sshUserPrivateKey(credentialsId: SSH_CREDENTIALS_ID, keyFileVariable: 'SSH_KEY', usernameVariable: 'SSH_USER')]) {
                    script {
                        // Restart Tomcat after deployment
                        sh """
                            ssh -o StrictHostKeyChecking=no -i \$SSH_KEY ${SSH_USER}@${TOMCAT_SERVER} 'systemctl restart tomcat'
                        """
                    }
                }
            }
        }
    }
}
