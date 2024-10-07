@Library('ls-shared-library') _
pipeline {
    agent {label 'demo'}
/*        triggers {
        genericTrigger(
            causeString: 'Triggered by webhook',
            token: 'my-webhook-token',  // Use this token for webhook security
            printPostContent: true
        )
    }
    */
    stages {
        stage('Hello') {
            steps {
                script {
                    trunkPipeline()
                }
            }
        }
    }
}
