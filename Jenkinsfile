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

        stage("Install Node") {
            steps {
                sh '''
                curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
                export NVM_DIR="$HOME/.nvm"
                [ -s "$NVM_DIR/nvm.sh" ] && . "$NVM_DIR/nvm.sh"
                nvm install 18
                nvm use 18
                node -v
                npm -v
                '''
            }
        }

        stage("Install dependencies") {
            steps {
                sh '''
                export NVM_DIR="$HOME/.nvm"
                [ -s "$NVM_DIR/nvm.sh" ] && . "$NVM_DIR/nvm.sh"
                nvm use 18
                npm install
                '''
            }
        }

        stage("Run tests") {
            steps {
                sh '''
                export NVM_DIR="$HOME/.nvm"
                [ -s "$NVM_DIR/nvm.sh" ] && . "$NVM_DIR/nvm.sh"
                nvm use 18
                npm test || true
                '''
            }
        }

        stage("Build") {
            steps {
                sh '''
                export NVM_DIR="$HOME/.nvm"
                [ -s "$NVM_DIR/nvm.sh" ] && . "$NVM_DIR/nvm.sh"
                nvm use 18
                npm run build
                '''
            }
        }

        stage("Deploy to Render") {
            steps {
                sh '''
                echo "Triggering deploy for gallery..."
                curl -X POST "https://api.render.com/v1/services/${SERVICE_ID}/deploys" \
                     -H "Authorization: Bearer ${RENDER_API_KEY}" \
                     -H "Content-Type: application/json" \
                     -d '{}'
                '''
            }
        }
    }

    post {
        success {
            sh "curl -X POST -H 'Content-type: application/json' --data '{\"text\":\"Build SUCCESS #${BUILD_NUMBER} - ${SITE_URL}\"}' $SLACK_WEBHOOK"
        }
        failure {
            sh "curl -X POST -H 'Content-type: application/json' --data '{\"text\":\"Build FAILED #${BUILD_NUMBER}\"}' $SLACK_WEBHOOK"
        }
    }
}
