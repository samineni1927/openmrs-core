pipeline {
    agent { label 'buildtool' }
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

        // stage('reporting') {
        //     steps {
        //         junit testResults: 'target/surefire-reports/*.xml'
        //     }
        // }

        stage('Artifactory') {
            steps {             
                rtMavenResolver (
                    id: 'openmrs-resolver-unique-id',
                    serverId: 'openmrs-id',
                    releaseRepo: 'openmrsmaven-libs-release-local',
                    snapshotRepo: 'openmrsmaven-libs-snapshot-local'
                )  

                rtMavenDeployer (
                    id: 'openmrs-deployer-unique-id',
                    serverId: 'openmrs-id',
                    releaseRepo: 'openmrsmaven-libs-release-local',
                    snapshotRepo: 'openmrsmaven-libs-snapshot-local',
                )

                // rtMavenRun (
                //     environment {
                //         MAVEN_HOME = '/usr/share/maven'
                //     }
                //     useWrapper: true,
                //     pom: 'maven-example/pom.xml',
                //     goals: 'clean install',
                //     resolverId: 'openmrs-resolver-unique-id',
                //     deployerId: 'openmrs-deployer-unique-id'
                    
                // )

            }
        }
        stage ('publishing build info') {
            steps {
                rtPublishBuildInfo (
                    serverId: 'openmrs-id',
                )

                rtDownload (
                    serverId: 'openmrs-id',
                    specPath: 'path/to/spec/relative/to/workspace/spec.json'
                )

                rtUpload (
                    serverId: 'openmrs-id',
                    specPath: 'path/to/spec/relative/to/workspace/spec.json'

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
