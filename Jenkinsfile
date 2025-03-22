pipeline {
    agent any
    
    environment {
        // Set environment variables
        NEXUS_URL = "https://nexus-cicd-prod.apps.ocptest.demo.local:8081/repository/maven-releases/"
        MVN_REPO = "maven-releases"
        GROUP_ID = "com.example"
        ARTIFACT_ID = "my-app"
        VERSION = "1.0.0"
        PACKAGING = "jar"
    }

    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/mrtlenovo/sample-ci.git'
            }
        }

        stage('Compile') {
            steps {
                script {
                    tool name: 'Maven', type: 'maven'
                    tool name: 'JDK', type: 'jdk'
                }
                sh 'mvn clean compile'
            }
        }

        stage('Package') {
            steps {
                script {
                    tool name: 'Maven', type: 'maven'
                    tool name: 'JDK', type: 'jdk'
                }
                sh 'mvn package'
            }
        }

        stage('Upload Artifact to Nexus') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'nexus-creds', usernameVariable: 'NEXUS_USERNAME', passwordVariable: 'NEXUS_PASSWORD')]) {
                    script {
                        tool name: 'Maven', type: 'maven'
                        tool name: 'JDK', type: 'jdk'
                    }
                    sh """
                        mvn deploy:deploy-file \
                        -DgroupId=${GROUP_ID} \
                        -DartifactId=${ARTIFACT_ID} \
                        -Dversion=${VERSION} \
                        -Dpackaging=${PACKAGING} \
                        -Dfile=target/sample-app-1.0-SNAPSHOT.jar \
                        -DrepositoryId=${MVN_REPO} \
                        -Durl=${NEXUS_URL} \
                        -DgeneratePom=true
                    """
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline execution completed.'
        }
        failure {
            echo 'Pipeline failed. Please check the logs.'
        }
    }
}
