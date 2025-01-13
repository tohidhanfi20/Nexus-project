pipeline {
    agent any

    tools {
        maven 'maven'
    }

    environment {
        WAR_NAME = 'forest-1.0-SNAPSHOT.war'
        NEXUS_URL = 'http://13.232.84.241:8081/repository/maven-releases/'
        NEXUS_CREDENTIALS_ID = 'nexus-cred'
        MAVEN_SETTINGS = 'settings.xml' // Path to your custom settings.xml for Maven
    }

    stages {
        stage('Checkout Code from GitHub') {
            steps {
                git credentialsId: 'git-cred', url: 'https://github.com/tohidhanfi20/Nexus-project.git', branch: 'main'
            }
        }

        stage('Build and Deploy WAR to Nexus') {
            steps {
                withCredentials([usernamePassword(credentialsId: NEXUS_CREDENTIALS_ID, usernameVariable: 'NEXUS_USER', passwordVariable: 'NEXUS_PASS')]) {
                    script {
                        // Run Maven build and deploy to Nexus
                        sh """
                            mvn -s ${MAVEN_SETTINGS} clean deploy
                        """
                    }
                }
            }
        }
    }
}
