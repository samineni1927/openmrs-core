pipeline {
    agent { label 'jenkins' }
    options {
        timeout(time:1, unit: 'HOURS')
    }

    triggers {
        cron ('H 2 * * *')
    }

    parameters {
        choice(name: 'GOAL', choices: ['compile', 'package', 'clean package'])
    }

    stages {
        stage('source code') {
            steps {
                git url: 'https://github.com/samineni1927/openmrs-core',
                branch: 'feature'
            }
        }

        stage('Build the code and sonar analysis') {
            steps {
                withSonarQubeEnv('sonar') {
                    sh "mvn ${params.GOAL} sonar:sonar"
                }
            }
        }

        stage('reporting') {
            steps {
                junit testResults: '**/surefire-reports/*.xml'
            }
        }



    }
    post {
        success {
            // send the success email
            echo "Success"
            mail bcc: '', body: "BUILD URL: ${BUILD_URL} TEST RESULTS ${RUN_TESTS_DISPLAY_URL} ", cc: '', from: 'samineni114@sami.com', replyTo: '', 
                subject: "${JOB_BASE_NAME}: Build ${BUILD_ID} Succeded", to: 'samineni114@gmail.com'
        }
        unsuccessful {
            //send the unsuccess email
            mail bcc: '', body: "BUILD URL: ${BUILD_URL} TEST RESULTS ${RUN_TESTS_DISPLAY_URL} ", cc: '', from: 'samineni114@sami.com', replyTo: '', 
                subject: "${JOB_BASE_NAME}: Build ${BUILD_ID} Failed", to: 'samineni114@gmail.com'
        }
    }

}
