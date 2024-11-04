pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('List Files') {
            steps {
                script {
                    sh 'ls -R'  // This will recursively list all files and directories in the workspace
                }
            }
        }
        stage('Run Self Loop Test') {
                    parallel {
                        stage('Run Specmatic as Stub') {
                            steps {
                                script {
                                    catchError(buildResult: 'SUCCESS', stageResult: 'SUCCESS') {
                                        sh """
                                        java -jar /usr/src/app/specmatic.jar stub
                                        """

                                    }
                                }
                            }
                        }
                        stage('Run Specmatic test') {
                            steps {
                                script {
                                    sleep 10
                                    sh """
                                    java -jar /usr/src/app/specmatic.jar test
                                    pkill -f 'java -jar'
                                    """
                                    }
                                }
                            }
                        }
                    }
                }

        stage('Run OpenAPI Examples Validation Check') {
                    steps {
                        script {
                            // Run the Specmatic OpenAPI Examples validation check inside a Docker container
                            sh '''
                                java -jar /usr/src/app/specmatic.jar examples validate \
                                --contract-file ./orders/product_search_bff_v4.yaml
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
