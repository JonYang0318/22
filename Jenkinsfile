pipeline {
    agent any
    
    environment {
        BUILD_USER = ''
        CHANNEL_ACCESS_TOKEN = "V1sQBsWv3nMo1Xn113ALhxRECcdMwBTs3saVWjLzPbudcKesfS7KTNrSExorYGB1b4Q4FUMm7Pmp6e/oPcYke2TSShO6mdBtfowe5FQJ+aOEDutPtOKclPzT1mXe0gc4N011Tsp6jVKNf1sKUtpd4QdB04t89/1O/w1cDnyilFU="
        USER_ID = "Ue464dafed0dd29b2030afbbd2aa7eaac"
    }
    
    parameters {
        string(name: 'SPEC', defaultValue: 'cypress/e2e/**', description: 'E.g., cypress/e2e/*.spec.js')
        choice(name: 'BROWSER', choices: ['chrome', 'edge'], description: 'Pick the web browser you want to use to run your scripts')
    }
    
    options {
        ansiColor('xterm')
    }

    stages {
        
        stage('Build'){ //建立
            steps {
                echo "Building the application"
            }
        }
        
        stage('Testing') {//測試
            steps {
                bat "npm i"
                bat 'npx cypress run --browser ${BROWSER} --spec ${SPEC} --reporter mochawesome --reporter-options "reportDir=cypress/custom-report"'

            }
        }
        
        stage('Deploy'){ //建置成功
            steps {
                echo "Deploying"
            }
        }
    }

   post {
    always {
        script {
            def REPORT_PATH = env.REPORT_PATH ?: 'cypress/report/mochawesome-report/mochawesome.html'
            def REPORT_NAME = env.REPORT_NAME ?: 'Mochawesome Report'
            def REPORT_TITLE = env.REPORT_TITLE ?: 'Cypress Test Report'

            // Upload Mochawesome report to Line Bot
            sh "curl -X POST -H 'Content-Type: application/json' -H 'Authorization: Bearer ${CHANNEL_ACCESS_TOKEN}' -d '{\"to\":\"${USER_ID}\",\"messages\":[{\"type\":\"text\",\"text\":\"${REPORT_TITLE}\"},{\"type\":\"image\",\"originalContentUrl\":\"${env.BUILD_URL}${REPORT_PATH}\",\"previewImageUrl\":\"${env.BUILD_URL}${REPORT_PATH}\"}]}' https://api.line.me/v2/bot/message/push"

            publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: true, reportDir: 'cypress/reports', reportFiles: 'index.html', reportName: 'HTML Report', reportTitles: ''])

            deleteDir()
        }
    }
}
}