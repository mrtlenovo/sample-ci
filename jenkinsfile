pipeline {
    agent any

    tools {
        // Define the Maven and Java tools to be able to run their commands
        jdk 'jdk11'
        maven 'maven'
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
    }
}
