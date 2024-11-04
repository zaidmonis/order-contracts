pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                echo 'Building...'
                // Add build commands here, for example:
                // sh './gradlew build'
            }
        }

        stage('Test') {
            steps {
                echo 'Testing...'
                // Add test commands here, for example:
                // sh './gradlew test'
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying...'
                // Add deployment commands here, if needed
            }
        }
    }
}
