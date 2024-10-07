@Library('ls-shared-library') _ 
pipeline {
    agent {label 'demo'}
/*        triggers {
        genericTrigger(
            causeString: 'Triggered by webhook',
            token: 'my-webhook-token',  // Use this token for webhook security
            printPostContent: true

    }
    */
    parameters {
        string(name: 'REPO_NAME')
        choice(name: 'ENVIRONMENT', choices: ['dev', 'stage', 'prod'])
    }
    environment {
        ENVIRONMENT = ''
        ISSUE_NUMBER = "${env.CHANGE_BRANCH ?: 'unknown'}"
        DESTINATION_BRANCH = "${env.GIT_BRANCH ?: 'unknown'}"
        tierlist = ''
    }
    stages {
        stage('Extract Feature Number') {
            steps {
                script {
                    // Regex to extract the feature name from a branch like 'feature/my-feature'
                    def branchName = env.SOURCE_BRANCH
                    def matcher = branchName =~ /(?:feat|hotfix)\/(.*)/
                    def extractedFeature = ''

                    if (matcher.matches()) {
                        extractedFeature = matcher[0][1] // Extract the value from the capturing group
                        echo "Extracted feature name: ${extractedFeature}"
                    } else {
                        echo "No match found in branch: ${branchName}"
                    }
                    // Set the extracted value as an environment variable for subsequent stages
                    env.FEATURE_NAME = extractedFeature
                }
            }
        }
        stage('Extract Env') {
            steps {
                script {
                    switch (env.DESTINATION_BRANCH) {
                        case 'dev':
                            ENVIRONMENT = 'dev'
                            break
                        case 'stage':
                            ENVIRONMENT = 'stage'
                            break
                        case 'main':
                            ENVIRONMENT = 'prod'
                            break
                        default:
                            error "Branch ${env.DESTINATION_BRANCH} is not allowed for deployment. Aborting."
                    }
                    echo "Deploying to environment: ${ENVIRONMENT}"
                }
            }
        }
        stage('Create Dependencies') {
            steps {
                script {
                    env.tierlist = createTierList()
                    echo "Tierlist: ${tierlist}"
                }
            }
        }
        // option - template & modify the tiered pipeline jenkinsfile
        // then trigger it using the next stage to keep it stateful
        stage('Template Tiered Pipeline') {
            steps {
                sh 'p'
            }
        }
        stage('Execute Tierlist') {
            steps {
                script {
                    build job: 'TIERED_PIPELINE', parameters: [
                        string(name: 'TIER_LIST', value: tierlist.toString())
                    ], wait: true
                }
            }
        }
        stage('run smoketest') {
            steps {
                runSmokeTest()
            }
        }
    }
    post {
        success {
            echo 'yes'
        }
        failure {
            script {
                build job: 'ROLLBACK', parameters: [
                    string(name: 'ENVIRONMENT', value: params.ENVIRONMENT)
                ], wait: true
            }
        }
        cleanup {
            echo 'splash'
        }
    }
}
