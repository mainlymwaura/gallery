pipeline {
    agent any

    environment {
        SLACK_WEBHOOK = credentials("slack-webhook")    
        SITE_URL = "https://gallery-1-628g.onrender.com/"
        RENDER_API_KEY = credentials("render-api-key")          
        SERVICE_ID = "srv-d4m508uuk2gs738o4670"                
    }

    stages {

        stage("Clone repository") {
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
                sh "echo 'Skipping tests for now'"
            }
        }

        stage("Build") {
            steps {
                sh "npm run build"
            }
        }

        stage("Deploy to Render") {
            steps {
                sh """
                echo "Triggering deploy for gallery-1..."
                curl -v -X POST "https://api.render.com/v1/services/${SERVICE_ID}/deploys" \
                  -H "Authorization: Bearer ${RENDER_API_KEY}" \
                  -H "Content-Type: application/json" \
                  -d '{}'
                """
            }
        }
    }

    post {
        success {
            script {
                def commit = sh(script: "git log -1 --pretty=format:'%h - %s'", returnStdout: true).trim()
                def user = sh(script: "git log -1 --pretty=format:'%an'", returnStdout: true).trim()

                sh """
                curl -X POST -H 'Content-Type: application/json' \
                  --data '{
                    "attachments": [
                      {
                        "color": "#36a64f",
                        "title": "Build SUCCESS :rocket:",
                        "fields": [
                          { "title": "Job", "value": "${env.JOB_NAME}", "short": true },
                          { "title": "Build #", "value": "${env.BUILD_NUMBER}", "short": true },
                          { "title": "Triggered By", "value": "${user}", "short": true },
                          { "title": "Commit", "value": "${commit}", "short": false },
                          { "title": "Build URL", "value": "${env.BUILD_URL}", "short": false },
                          { "title": "Live Site", "value": "${SITE_URL}", "short": false }
                        ]
                      }
                    ]
                  }' \
                  $SLACK_WEBHOOK
                """
            }
        }
        failure {
            script {
                def commit = sh(script: "git log -1 --pretty=format:'%h - %s'", returnStdout: true).trim()
                def user = sh(script: "git log -1 --pretty=format:'%an'", returnStdout: true).trim()

                sh """
                curl -X POST -H 'Content-Type: application/json' \
                  --data '{
                    "attachments": [
                      {
                        "color": "#ff0000",
                        "title": "Build FAILED :warning:",
                        "fields": [
                          { "title": "Job", "value": "${env.JOB_NAME}", "short": true },
                          { "title": "Build #", "value": "${env.BUILD_NUMBER}", "short": true },
                          { "title": "Triggered By", "value": "${user}", "short": true },
                          { "title": "Commit", "value": "${commit}", "short": false },
                          { "title": "Build URL", "value": "${env.BUILD_URL}", "short": false }
                        ]
                      }
                    ]
                  }' \
                  $SLACK_WEBHOOK
                """
            }
        }
    }
}
