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
                    sh 'ls -R'
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
                    def githubToken = env.GH_REPOSITORY_TOKEN
                    def orgId = '66fe6c555e232d36a28fef94'
                    def branchRef = env.GIT_BRANCH
                    def branchName = env.GIT_BRANCH
                    def buildId = env.BUILD_ID
                    def repoName = env.GIT_REPO_NAME
                    def repoId = env.GIT_REPO_ID
                    def repoUrl = env.GIT_URL

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
