@Library('ls-shared-library') _ 
import groovy.json.JsonOutput
pipeline {
    agent {label 'demo'}
    parameters {
        string(name: "COMPONENT", defaultValue: '', description: 'Component name')
        string(name: "ENVIRONMENT", defaultValue: '', description: 'Environment to deploy to')
        string(name: "BUMP", defaultValue: 'patch', description: 'Version bump type (major, minor, patch)')
        string(name: "FEAT_NUM", defaultValue: '', description: 'Feature number')
        booleanParam(name: 'DEBUG', defaultValue: false, description: 'Enable debug mode')
    }
    stages {
        stage('Check Feature Lock') {
            when {
                expression { return params.ENVIRONMENT == 'dev'}
            }
            steps {
                script {
                    if (checkFeatureLock(issue: env.FEAT_NUM)) {
                        catchError(buildResult: 'ABORTED', stageResult: 'ABORTED') {
                            error("Feature ${env.FEAT_NUM} is locked. Aborting...")
                        }
                    } else {
                        echo "Feature ${env.FEAT_NUM} is unlocked. Proceeding..."
                    }
                }
                echo 'Lock Free!'
            }
        }
        stage('Create Dependencies') {
            agent {
                label 'python'
            }
            steps {
                script {
                    createTierList(env: params.ENVIRONMENT, repo: params.COMPONENT, DEBUG: params.DEBUG)
                    if (params.DEBUG) {
                        tierList = readFile 'tierList.json'
                        echo "Tierlist: ${JsonOutput.prettyPrint(tierList)}"
                    }
                }
                echo 'Dependecies Created!'
            }
        }
        // option - template & modify the tiered pipeline jenkinsfile
        // then trigger it using the next stage to keep it stateful
        stage('Template Tiered Pipeline') {
            agent {
                label 'python'
            }
            steps {
                script {
                    def tierList = readFile 'tierList.json'
                    if (params.DEBUG) {
                        echo "Tierlist: ${JsonOutput.prettyPrint(tierList)}"
                    }
                    //createTieredPipeline(tierlist: env.tierList, env: env.ENVIRONMENT)
                    echo 'Tiered Pipeline Templated!'
                }
            }
        }
        stage('Execute Tierlist') {
            steps {
                /*build job: 'ls-cicd-tiered-pipeline', // Job name in Jenkins for the tiered pipeline
                    parameters: [
                        string(name: 'ENVIRONMENT', value: env.ENVIRONMENT), // Pass the environment param
                        string(name: 'BUILD_ID', value: params.BUILD_ID) // Pass the build ID or version
                    ],
                    propagate: true, // Fail this job if the triggered job fails
                    wait: true // Wait for the triggered job to finish
                */
                echo 'TierList Executed!'
            }
        }
        stage('run smoketest') {
            steps {
                //runSmokeTest()
                echo 'Smoke Test Ran!'
            }
        }
    }
    post {
        success {
            echo 'yas'
        }
        failure {
            /*script {
                build job: 'ls-cicd-rollback', parameters: [
                    string(name: 'ENVIRONMENT', value: params.ENVIRONMENT)
                ], wait: false
            }*/
            echo 'fail'
        }
        cleanup {
            echo 'splash'
        }
    }
}
