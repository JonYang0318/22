pipeline {
    agent any

    environment {
        CHANNEL_ACCESS_TOKEN = 'V1sQBsWv3nMo1Xn113ALhxRECcdMwBTs3saVWjLzPbudcKesfS7KTNrSExorYGB1b4Q4FUMm7Pmp6e/oPcYke2TSShO6mdBtfowe5FQJ+aOEDutPtOKclPzT1mXe0gc4N011Tsp6jVKNf1sKUtpd4QdB04t89/1O/w1cDnyilFU=',
        USER_ID = 'Ue464dafed0dd29b2030afbbd2aa7eaac'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                bat 'npm install'
            }
        }
stage('Testing') {
    steps {
        script {
            def browser = 'edge' // You can customize this
            def spec = 'path/to/your/specs/*.spec.js' // You can customize this

            bat "npx cypress run --browser ${browser} --spec ${spec} --reporter mochawesome --reporter-options reportDir=cypress/custom-report"
        }
            }
        }
    }

    post {
        always {
            script {
                def REPORT_PATH = 'cypress/custom-report/mochawesome.html'
                def REPORT_TITLE = 'Cypress Test Report'

                // Upload Mochawesome report to LINE Bot
                sh "curl -X POST -H 'Content-Type: application/json' -H 'Authorization: Bearer ${CHANNEL_ACCESS_TOKEN}' -d '{\"to\":\"${USER_ID}\",\"messages\":[{\"type\":\"text\",\"text\":\"${REPORT_TITLE}\"},{\"type\":\"image\",\"originalContentUrl\":\"${BUILD_URL}${REPORT_PATH}\",\"previewImageUrl\":\"${BUILD_URL}${REPORT_PATH}\"}]}' https://api.line.me/v2/bot/message/push"

                // Clean up workspace
                deleteDir()
            }
        }
    }
}