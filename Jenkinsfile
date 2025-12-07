pipeline {
    agent any

    environment {
        SLACK_WEBHOOK = credentials("slack-webhook")
        SITE_URL = "https://gallery-1-628g.onrender.com/"
        RENDER_HOOK = "https://api.render.com/deploy/srv-cugv5t08ph6s73fb7prg?key=j77miZzMSsM"
    }

    stages {
        stage("Clone repository") {
            steps {
                git branch: "master", url: "https://github.com/mainlymwaura/gallery.git"
            }
        }

        stage("Ensure Node & Install dependencies") {
            steps {
                sh """
                # Install Node if not present (optional, if your agent lacks Node)
                if ! command -v node > /dev/null; then
                  curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
                  sudo apt-get install -y nodejs
                fi
                npm install
                """
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
                curl -X POST -H 'Content-Type: application/json' \
                  -d '{ "trigger": "jenkins" }' \
                  "$RENDER_HOOK"
                """
            }
        }
    }

    post {
        success {
            script {
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
