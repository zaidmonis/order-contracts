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
        stage('RGenerate central contract repo report') {
            steps {
                script {
                    sh '''
                        java -jar /usr/src/app/specmatic.jar central-contract-repo-report
                    '''
                }
            }
        }

        stage('Run Specmatic Insights Reporter') {
            steps {
                script {
                    // Set variables for the required inputs
                    def githubToken = env.GH_REPOSITORY_TOKEN // Make sure this is set as a Jenkins secret
                    def orgId = '66fe6c555e232d36a28fef94'
                    def branchRef = env.GIT_BRANCH // Jenkins automatically sets this environment variable
                    def branchName = env.GIT_BRANCH // Alternatively, you can use another method to derive this
                    def buildId = env.BUILD_ID
                    def repoName = env.GIT_REPO_NAME // You may need to set this as an environment variable in Jenkins
                    def repoId = env.GIT_REPO_ID // You may need to set this as an environment variable in Jenkins
                    def repoUrl = env.GIT_URL // This will contain the repository URL

                    // Prepare the JSON payload for the API request
                    def jsonPayload = """
                    {
                        "github-token": "${githubToken}",
                        "org-id": "${orgId}",
                        "branch-ref": "${branchRef}",
                        "branch-name": "${branchName}",
                        "build-id": "${buildId}",
                        "repo-name": "${repoName}",
                        "repo-id": "${repoId}",
                        "repo-url": "${repoUrl}"
                    }
                    """

                    // Make the POST request to the Specmatic Insights API
                    sh """
                    curl -X POST -H "Content-Type: application/json" \
                    -d '${jsonPayload}' \
                    https://insights.specmatic.io/report
                    """
                }
            }
        }
    }
}
