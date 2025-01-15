pipeline {
    agent any

    tools {
        maven 'maven'
    }

    environment {
        WAR_NAME = 'forest-1.0-SNAPSHOT.war'
        NEXUS_URL = 'http://13.232.84.241:8081/repository/maven-releases/'
        NEXUS_CREDENTIALS_ID = 'nexus-cred'
        TOMCAT_CREDENTIALS_ID = 'tomcat-ssh-credentials'  // Reference your Tomcat credentials
        MAVEN_SETTINGS = 'settings.xml' // Path to your custom settings.xml for Maven
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

        stage('Deploy WAR to Tomcat') {
            steps {
                withCredentials([usernamePassword(credentialsId: TOMCAT_CREDENTIALS_ID, usernameVariable: 'TOMCAT_USER', passwordVariable: 'TOMCAT_PASS')]) {
                    script {
                        // Safely pass the password using an environment variable
                        sh """
                            sshpass -p \$TOMCAT_PASS scp target/${WAR_NAME} \$TOMCAT_USER@15.207.112.237:/root/tomcat/webapps/
                        """
                    }
                }
            }
        }
    }
}
