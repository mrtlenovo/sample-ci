pipeline {
    agent any
    
    environment {
        GROUP_ID = 'com.example'
        ARTIFACT_ID = 'my-app'
        VERSION = '1.0.0'
        PACKAGING = 'jar'
        MVN_REPO = 'maven-releases'
        NEXUS_URL = 'https://nexus-cicd-prod.apps.ocptest.demo.local:8081/repository/maven-releases/'
        NEXUS_CREDENTIALS_ID = 'nexus-creds'
    }
    
    tools {
        maven 'maven'    // Replace 'maven' with the exact Maven installation name from Global Tool Configuration
        jdk 'jdk11'      // Replace 'jdk11' with the exact JDK installation name
    }

    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/mrtlenovo/sample-ci.git'
            }
        }

        stage('Compile') {
            steps {
                sh 'mvn clean compile'
            }
        }

        stage('Package') {
            steps {
                sh 'mvn package'
            }
        }

        stage('Upload Artifact to Nexus') {
            steps {
                withCredentials([usernamePassword(credentialsId: "${NEXUS_CREDENTIALS_ID}",
                                                 usernameVariable: 'NEXUS_USERNAME',
                                                 passwordVariable: 'NEXUS_PASSWORD')]) {
                    sh '''mvn deploy:deploy-file \
                        -DgroupId=${GROUP_ID} \
                        -DartifactId=${ARTIFACT_ID} \
                        -Dversion=${VERSION} \
                        -Dpackaging=${PACKAGING} \
                        -Dfile=target/sample-app-1.0-SNAPSHOT.jar \
                        -DrepositoryId=${MVN_REPO} \
                        -Durl=${NEXUS_URL} \
                        -DgeneratePom=true
                    '''
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline execution completed.'
        }
        success {
            echo 'Pipeline completed successfully.'
        }
        failure {
            echo 'Pipeline failed. Please check the logs.'
        }
    }
}
