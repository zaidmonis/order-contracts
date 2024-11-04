pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Run OpenAPI Examples Validation Check') {
                    steps {
                        script {
                            // Run the Specmatic OpenAPI Examples validation check inside a Docker container
                            sh '''
                                java -jar /usr/src/app/specmatic.jar examples validate \
                                --contract-file /central-contract-repo/orders/product_search_bff_v4.yaml
                            '''
                        }
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
