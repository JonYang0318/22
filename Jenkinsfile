pipeline {
  agent any

  environment {
    BUILD_USER = ''
    LINE_NOTIFY_TOKEN = "V1sQBsWv3nMo1Xn113ALhxRECcdMwBTs3saVWjLzPbudcKesfS7KTNrSExorYGB1b4Q4FUMm7Pmp6e/oPcYke2TSShO6mdBtfowe5FQJ+aOEDutPtOKclPzT1mXe0gc4N011Tsp6jVKNf1sKUtpd4QdB04t89/1O/w1cDnyilFU="
  }

  parameters {
    string(name: 'SPEC', defaultValue: 'cypress/e2e/Telligent/**', description: 'Ej: cypress/e2e/**/*.spec.js')
    choice(name: 'BROWSER', choices: ['chrome', 'edge'], description: 'Pick the web browser you want to use to run your scripts')
  }

  options {
    ansiColor('xterm')
  }

  stages {
    stage('Build') {
      steps {
        echo "Building the application"
      }
    }

    stage('Testing') {
      steps {
        bat "npm i"
        bat 'npx cypress run --browser ${BROWSER} --spec ${SPEC} --reporter mochawesome --reporter-options "reportDir=cypress/custom-report"'
      }
    }

    stage('Deploy') {
      steps {
        echo "Deploying"
      }
    }
  }

  post {
    always {
      script {
        BUILD_USER = getBuildUser()
      }

      // Upload Mochawesome report to LINE Notify
      sh """
        curl -X POST https://notify-api.line.me/api/notify \
            -H 'Authorization: Bearer ${LINE_NOTIFY_TOKEN}' \
            -H 'Content-Type: application/x-www-form-urlencoded' \
            -d "message=${currentBuild.currentResult}: Job ${env.JOB_NAME} build ${env.BUILD_NUMBER} by ${BUILD_USER}\n Tests:${SPEC} executed at ${BROWSER} \n More info at: ${env.BUILD_URL}HTML_20Report/"
      """

      publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: true, reportDir: 'cypress/report', reportFiles: 'index.html', reportName: 'HTML Report', reportTitles: ''])
      deleteDir()
    }
  }
}
