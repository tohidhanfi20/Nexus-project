pipeline {
    agent any

    tools {
        maven 'maven'
    }

    environment {
        WAR_NAME = 'forest-1.0-SNAPSHOT.war'
        NEXUS_URL = 'http://13.232.84.241:8081/repository/maven-releases/'
        NEXUS_CREDENTIALS_ID = 'nexus-cred'
        TOMCAT_CREDENTIALS_ID = 'tomcat-ssh-credentials'
        MAVEN_SETTINGS = 'settings.xml'
    }

    stages {
        stage('Checkout Code from GitHub') {
            steps {
                git credentialsId: 'git-cred', url: 'https://github.com/tohidhanfi20/Nexus-project.git', branch: 'main'
            }
        }

        stage('Build WAR File') {
            steps {
                script {
                    sh """
                        mvn -s ${MAVEN_SETTINGS} clean package
                    """
                }
            }
        }

        stage('Store WAR file to Nexus') {
            steps {
                withCredentials([usernamePassword(credentialsId: NEXUS_CREDENTIALS_ID, usernameVariable: 'NEXUS_USER', passwordVariable: 'NEXUS_PASS')]) {
                    script {
                        sh """
                            mvn -s ${MAVEN_SETTINGS} deploy
                        """
                    }
                }
            }
        }

        stage('Deploy Application to Tomcat') {
            steps {
                withCredentials([sshUserPrivateKey(credentialsId: TOMCAT_CREDENTIALS_ID, keyFileVariable: 'SSH_PRIVATE_KEY')]) {
                    script {
                        // Deploy WAR to Tomcat server
                        sh """
                            sshpass -p \$SSH_PRIVATE_KEY ssh -o StrictHostKeyChecking=no root@15.207.112.237 'scp target/${WAR_NAME} /root/tomcat/webapps/'
                        """
                    }
                }
            }
        }
    }
}
