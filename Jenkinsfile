pipeline{
    agent any
    environment {
    SLACK_WEBHOOK = credentials("slack-webhook")
    SITE_URL = "https://gallery-1-628g.onrender.com/"
        }

    stages{
        stage("Cloning repo"){
            steps{
                git branch:"master",url:"https://github.com/mainlymwaura/gallery.git"
            }
        }
        stage("Install") {
            steps {
                sh "npm install"
            }
        }

        stage('Test') {
            steps {
                sh 'npm test'
            }
        }

        stage("Build") {
            steps {
                sh "npm run build"
        
            }
        }

        stage("Deploy") {
            steps {
                echo "deployed successfully"
            }
        }


    }     
}
          