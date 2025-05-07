pipeline {
   agent any

    environment {
        IMAGE_NAME = 'coral-proj-project'
        TAG = ${{ steps.vars.outputs.SHORT_SHA }}
        PROJECT_ID = "coral-proj-project"
    }
    }
    
    triggers {
        GenericTrigger(
                genericVariables: [
                        [
                                key           : 'Build_Docker_image',
                                value         : '$.imageTag',
                                expressionType: 'JSONPath'
                        ],
                ],
                token: '111e6414880e3656c13aa4d3e072aad24f',
                causeString: 'Generic Cause',
                printContributedVariables: true,
                printPostContent: true,
                silentResponse: false
        )
    }
    stages {
        stage('setup') {
            steps {
                withCredentials([file(credentialsId: 'coral-prod-sa', variable: 'GC_KEY')]) {
                    sh("gcloud auth activate-service-account --key-file=${GC_KEY}")
                    
                }
            }
        }
        stage('checkout') {
            steps {
                git branch: 'main', credentialsId: 'github-jenkins-bot', 
            }
        }
        }
        stage('Build Docker Image') {
            steps {
                sh './build.sh'
            }
        }
    }
  
}
