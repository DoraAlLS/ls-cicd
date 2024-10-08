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
    environment {
        ENVIRONMENT = ''
        REPO_NAME = env.GIT_REPO
        ISSUE_NUMBER = "${env.CHANGE_BRANCH ?: 'unknown'}"
        DESTINATION_BRANCH = "${env.GIT_BRANCH ?: 'unknown'}"
        tierList = ''
    }
    stages {
        stage('Extract Feature Number') {
            steps {
                /* script {
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
                } */
                echo 'filler'
            }
        }
        stage('Extract Env') {
            steps {
                /* script {
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
                } */
                echo 'filler2'
            }
        }
        stage('Create Dependencies') {
            steps {
                /* script {
                    env.tierList = createTierList()
                    echo "Tierlist: ${tierList}"
                } */
                echo 'filler3'
            }
        }
        // option - template & modify the tiered pipeline jenkinsfile
        // then trigger it using the next stage to keep it stateful
        stage('Template Tiered Pipeline') {
            steps {
                echo 'filler4'
            }
        }
        stage('Execute Tierlist') {
            steps {
                /*build job: 'ls-cicd-tiered-pipeline', // Job name in Jenkins for the tiered pipeline
                    parameters: [
                        string(name: 'BRANCH_NAME', value: 'main'), // Target branch for tiered-pipeline job
                        string(name: 'ENVIRONMENT', value: env.ENVIRONMENT), // Pass the environment param
                        string(name: 'BUILD_ID', value: params.BUILD_ID) // Pass the build ID or version
                    ],
                    propagate: true, // Fail this job if the triggered job fails
                    wait: true // Wait for the triggered job to finish
                */
                echo 'filler5'
            }
        }
        stage('run smoketest') {
            steps {
                //runSmokeTest()
                echo 'filler6'
            }
        }
    }
    post {
        success {
            echo 'yes'
        }
        failure {
            /*script {
                build job: 'ROLLBACK', parameters: [
                    string(name: 'ENVIRONMENT', value: params.ENVIRONMENT)
                ], wait: true
            }*/
            echo 'fail'
        }
        cleanup {
            echo 'splash'
        }
    }
}
