pipeline {
    //The agent section specifies where the entire Pipeline, or a specific stage, 
    //will execute in the Jenkins environment depending on where the agent section is placed.
    agent any
    
    //The environment directive specifies a sequence of key-value pairs which will be defined
    //as environment variables for all steps, or stage-specific steps, depending on where the environment directive is located within the Pipeline.
    environment {
        BUILD_USER = ''
    }
    
    //The parameters directive provides a list of parameters that a user should provide when triggering the Pipeline.
    //The values for these user-specified parameters are made available to Pipeline steps via the params object, see
    //the Parameters, Declarative Pipeline for its specific usage.
    parameters {
        string(name: 'SPEC', defaultValue: 'cypress/e2e/**', description: 'Ej: cypress/e2e/**/*.spec.js')
        choice(name: 'BROWSER', choices: ['chrome', 'edge', 'firefox'], description: 'Pick the web browser you want to use to run your scripts')
    }
    
    //The options directive allows configuring Pipeline-specific options from within the Pipeline itself.
    //Pipeline provides a number of these options, such as buildDiscarder, but they may also be provided by
    //plugins, such as timestamps. Ex: retry on failure
    options {
        ansiColor('xterm')
    }

    //The stage directive goes in the stages section and should contain a steps section, an optional agent section, 
    //or other stage-specific directives. Practically speaking, all of the real work done by a Pipeline will be wrapped
    //in one or more stage directives.
    stages {
        
        stage('Build'){
            //The steps section defines a series of one or more steps to be executed in a given stage directive.
            steps {
                echo "Building the application"
            }
        }
        
        stage('Testing') {
            steps {
                bat "npm i"
                bat "npx cypress run --browser ${BROWSER} --spec ${SPEC}"
            }
        }
        
        stage('Deploy'){
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

            // 用LINE Notify替换Slack通知
            // 确保将YOUR_LINE_NOTIFY_TOKEN替换为实际的LINE Notify令牌
            sh """
                curl -X POST \
                -H "Authorization: Bearer iRQ4HtmoqfgGaanKadUl9Bli0Ww93qpIZJ5g8aYCFlL" \
                -F "message=*${currentBuild.currentResult}:* Job ${env.JOB_NAME} build ${env.BUILD_NUMBER} by ${BUILD_USER} Tests:${SPEC} executed at ${BROWSER} More info at: ${env.BUILD_URL}HTML_20Report/" \
                https://notify-api.line.me/api/notify
            """

            // 发布HTML报告
            publishHTML([
            allowMissing: false,
            alwaysLinkToLastBuild: false,
            keepAll: true,
            reportDir: 'reports',  // 注意這裡指定的資料夾路徑要和實際生成報告的資料夾路徑一致
            reportFiles: 'index.html',
            reportName: 'HTML Report',
            reportTitles: ''
        ])

        // Clean up the workspace
        deleteDir()
    }
}