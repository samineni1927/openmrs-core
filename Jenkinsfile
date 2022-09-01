pipeline {
    agent { label 'buildtool' }
    options {
        timeout(time:1, unit: 'HOURS')
    }

    triggers {
        cron ('H 2 * * *')
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
                    sh "mvn clean package sonar:sonar"
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
