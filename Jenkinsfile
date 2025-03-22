pipeline {
    agent any

    tools {
        // Define the Maven and Java tools from Jenkins Global Tool Configuration
        jdk 'jdk11'
        maven 'maven'
    }

    environment {
        NEXUS_URL = 'https://nexus-cicd-prod.apps.ocptest.demo.local:8081/repository/maven-releases/'  // Nexus repository URL (Update accordingly)
        NEXUS_CREDENTIALS_ID = 'nexus-creds'  // Jenkins credentials ID for Nexus
        NEXUS_USER = admin
        NEXUS_PASSWORD = test@123
        MVN_REPO = 'maven-releases'  // Nexus repository name
        GROUP_ID = 'com.example'  // Maven Group ID
        ARTIFACT_ID = 'my-app'    // Artifact ID
        VERSION = '1.0.0'         // Artifact version
        PACKAGING = 'jar'         // Packaging type (e.g., jar, war)
    }

    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/mrtlenovo/sample-ci.git'
            }
        }

        stage('Compile') {
            steps {
                sh "mvn clean compile"
            }
        }

        stage('Package') {
            steps {
                sh "mvn package"
            }
        }

        stage('Upload Artifact to Nexus') {
            steps {
                // Upload the packaged artifact (JAR file) to Nexus
                withCredentials([usernamePassword(credentialsId: "${NEXUS_CREDENTIALS_ID}", 
                                                 usernameVariable: 'NEXUS_USER', 
                                                 passwordVariable: 'NEXUS_PASSWORD')]) {
                    sh """
                        mvn deploy:deploy-file \
                        -DgroupId=${GROUP_ID} \
                        -DartifactId=${ARTIFACT_ID} \
                        -Dversion=${VERSION} \
                        -Dpackaging=${PACKAGING} \
                        -Dfile=target/${ARTIFACT_ID}-${VERSION}.${PACKAGING} \
                        -DrepositoryId=${MVN_REPO} \
                        -Durl=${NEXUS_URL} \
                        -DgeneratePom=true \
                        -DrepositoryId=nexus-repo
                    """
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline execution completed.'
        }
        success {
            echo 'Artifact successfully uploaded to Nexus!'
        }
        failure {
            echo 'Pipeline failed. Please check the logs.'
        }
    }
}
