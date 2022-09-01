pipeline {
    agent { label 'buildtool' }
    options {
        timeout(time:1, unit: 'HOURS')
    }

    triggers {
        pollSCM ('H 2 * * *')
    }

    parameters {
        choice ( name: 'GOAL', choices: ['compile', 'pacakge', 'clean package'])
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
                withSonarQubeEnv(sonar) {
                    sh "mvn ${params.GOAL} sonar:sonar"
                }
            }
        }

        stage('reporting') {
            steps {
                junit testResults: 'target/surefire-reports/*.xml'
            }
        }

    }

}
