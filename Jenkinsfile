pipeline {
    agent {label 'demo'}
        triggers {
        genericWebhookTrigger(
            causeString: 'Triggered by webhook',
            token: 'my-webhook-token',  // Use this token for webhook security
            printPostContent: true
        )
    }
    stages {
        stage('Hello') {
            steps {
                script {
                    @Library('ls-shared-library')
                    trunkPipeline()
                }
            }
        }
    }
}
