pipeline {
    agent any

    tools {
        jdk 'jdk11'
        maven 'maven'
    }

    environment {
        NEXUS_URL = 'https://nexus-cicd-prod.apps.ocptest.demo.local:8081/repository/maven-releases/'
        NEXUS_CREDENTIALS_ID = 'nexus-creds'
        MVN_REPO = 'maven-releases'
        GROUP_ID = 'com.example'
        ARTIFACT_ID = 'sample-app'    // Updated to match the Maven project artifactId
        VERSION = '1.0-SNAPSHOT'      // Updated to match the Maven project version
        PACKAGING = 'jar'
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
                withCredentials([usernamePassword(credentialsId: "${NEXUS_CREDENTIALS_ID}", 
                                                 usernameVariable: 'NEXUS_USER', 
                                                 passwordVariable: 'NEXUS_PASSWORD')]) {
                    sh """
                        mvn deploy:deploy-file \
                        -DgroupId=${GROUP_ID} \
                        -DartifactId=${ARTIFACT_ID} \
                        -Dversion=${VERSION} \
                        -Dpackaging=${PACKAGING} \
                        -Dfile=target/${ARTIFACT_ID}-${VERSION}.${PACKAGING} \  # Updated to match actual file path
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
