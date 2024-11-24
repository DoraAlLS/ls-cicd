@Library('ls-shared-library') _ 
import groovy.json.JsonOutput
import groovy.json.JsonSlurper
pipeline {
    agent {
            label 'demo'
    }
    parameters {
        string(name: "COMPONENT", defaultValue: '', description: 'Component name')
        string(name: "ENVIRONMENT", defaultValue: '', description: 'Environment to deploy to')
        string(name: "BUMP", defaultValue: 'patch', description: 'Version bump type (major, minor, patch)')
        string(name: "FEAT_NUM", defaultValue: '', description: 'Feature number')
        booleanParam(name: 'DEBUG', defaultValue: true, description: 'Enable debug mode')
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
                    label 'python-linux'
            }
            steps {
                script {
                    if (params.DEBUG) {
                        echo "Component: ${params.COMPONENT}"
                        echo "Environment: ${params.ENVIRONMENT}"
                        echo "Bump: ${params.BUMP}"
                        echo "Feature Number: ${params.FEAT_NUM}"
                    }
                    createTierList(env: params.ENVIRONMENT, repo: params.COMPONENT, DEBUG: params.DEBUG)
                    if (params.DEBUG) {
                        def tierList = readFile file: 'tierList.json'
                        echo "Tierlist: ${JsonOutput.prettyPrint(tierList)}"
                    }
                    // Stash the JSON file for use in subsequent stages
                    stash includes: 'tierList.json', name: 'tierList'
                }
                echo 'Dependecies Created!'
            }
        }
        // option - template & modify the tiered pipeline jenkinsfile
        // then trigger it using the next stage to keep it stateful
        stage('Template Tiered Pipeline') {
            agent {
                    label 'python-linux'
            }
            steps {
                checkout scmGit (branches: [[name: 'main']], extensions: [], userRemoteConfigs: [[credentialsId: 'dor-github-jenkins-token', url: 'https://github.com/LightSolverInternal/ls-cicd-tiered-pipeline.git']])                
                script {
                    // Unstash the JSON file
                    unstash name: 'tierList'
                    def tierList = readFile 'tierList.json'
                    def parsedTierList = new groovy.json.JsonSlurper().parseText(tierList)
                    def compactTierList = JsonOutput.toJson(parsedTierList)
                    if (params.DEBUG) {
                        echo "Tierlist JSON String: ${tierList}"
                        echo "PrettyPrint Tierlist: ${JsonOutput.prettyPrint(tierList)}"
                        echo "parsed : ${parsedTierList}"
                        echo "compact : ${compactTierList}"
                    }
                    createTieredPipeline(tierlist: compactTierList, env: params.ENVIRONMENT, bump: params.BUMP, debug: params.DEBUG)
                    echo 'Tiered Pipeline Templated!'
                    sh """
                        git config --global user.email "jenkins-auto-push@lightsolver.com"
                        git config --global user.name "Jenkins Auto Push"
                        git add Jenkinsfile
                        git commit -m "Auto-generate Jenkinsfile for component ${params.COMPONENT} in environment ${params.ENVIRONMENT} feat ${params.FEAT_NUM}"
                        git push origin main
                    """
                }
            }
        }
        stage('Execute Tierlist') {
            steps {
                /*build job: 'LS-DLPU/ls-cicd-tiered-pipeline/main', // Job name in Jenkins for the tiered pipeline
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
