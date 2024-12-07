pipeline {
    agent any
    environment {
              APP_NAME = "account"
    }

    stages {
        stage("Cleanup Workspace") {
            steps {
                cleanWs()
            }
        }

        stage("Checkout from SCM") {
               steps {
                   git branch: 'main', credentialsId: 'git-credential', url: 'https://github.com/ghkimdev/account-CD.git'
               }
        }

        stage("Update the Deployment Tags") {
            steps {
                sh """
                   cat deployment.yaml
                   sed -i 's/${APP_NAME}.*/${APP_NAME}:${IMAGE_TAG}/g' deployment.yaml
                   cat deployment.yaml
                """
            }
        }

        stage("Push the changed deployment file to Git") {
            steps {
                sh """
                    git config --global user.name "ghkimdev"
                    git config --global user.email "ghkim.dev@gmail.com"
                    git add deployment.yaml
                    git commit -m "Update deployment.yaml"
                """
                withCredentials([gitUsernamePassword(credentialsId: 'git-credential', gitToolName: 'Default')]) {
                    git push https://github.com/ghkimdev/account-CD.git
                }
            }
        } 
    }
    post {
        success {
            slackSend channel: 'jenkins-noti', color: 'good', message: "${env.JOB_NAME} - #${env.BUILD_NUMBER} ${currentBuild.result} after ${currentBuild.durationString} sec (<${env.BUILD_URL}|Open>)", tokenCredentialId: 'slack-credential'
        }
        failure {
            slackSend channel: 'jenkins-noti', color: 'danger', message: "${env.JOB_NAME} - #${env.BUILD_NUMBER} ${currentBuild.result} after ${currentBuild.durationString} sec (<${env.BUILD_URL}|Open>)", tokenCredentialId: 'slack-credential'
        }
    }
}
