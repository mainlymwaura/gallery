pipeline {
    agent any

    tools {
        nodejs "node16"
    }

    environment {
        SLACK_WEBHOOK = credentials("slack-webhook")
        SITE_URL = "https://gallery-1-628g.onrender.com/"
        RENDER_DEPLOY_HOOK = credentials("render-webhook") 
    }

    stages {

        stage("Checkout") {
            steps {
                git branch: "master", url: "https://github.com/mainlymwaura/gallery.git"
            }
        }

        stage("Install dependencies") {
            steps {
                sh "npm install"
            }
        }

        stage("Run tests") {
            steps {
                sh "npm test"
            }
            post {
                failure {
                    mail to: "calebmwaura1@gmail.com",
                         subject: "Tests Failed on Jenkins",
                         body: "Your tests failed on build #${env.BUILD_NUMBER}."
                }
            }
        }

        stage("Build") {
            steps {
                sh "npm run build || echo 'No build script found'"
            }
        }

        stage("Deploy to Render") {
            steps {
                sh "wget -qO- $RENDER_DEPLOY_HOOK"
            }
        }
    }

    post {
        success {
            slackSend webhookUrl: SLACK_WEBHOOK,
                      message: "Build successful! Build #${env.BUILD_NUMBER}. Site: ${SITE_URL}"
        }

        failure {
            slackSend webhookUrl: SLACK_WEBHOOK,
                      message: "Build failed on Jenkins. Build #${env.BUILD_NUMBER}."
        }
    }
}
