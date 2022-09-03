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

    environment {
        MAVEN_HOME = '/usr/share/maven'
    }

    stages {
        stage('source code') {
            steps {
                git url: 'https://github.com/samineni1927/openmrs-core',
                branch: 'feature'
            }
        }

        // stage('Build the code and sonar analysis') {
        //     steps {
        //         withSonarQubeEnv('sonar') {
        //             sh "mvn ${params.GOAL} sonar:sonar"
        //         }
        //     }
        // }

        stage('reporting') {
            steps {
                // junit testResults: '**/api/target/surefire-reports/*.xml'
                // junit testResults: '**/liquibase/target/surefire-reports/*.xml'
                junit testResults: '**/web/target/surefire-reports/*.xml'
            }
        }

        stage('Artifactory') {
            steps {             
                rtMavenResolver (
                    id: 'openmrs-resolver-unique-id',
                    serverId: 'jfrog',
                    releaseRepo: 'openmrsmaven-libs-release-local',
                    snapshotRepo: 'openmrsmaven-libs-snapshot-local'
                )  

                rtMavenDeployer (
                    id: 'openmrs-deployer-unique-id',
                    serverId: 'jfrog',
                    releaseRepo: 'openmrsmaven-libs-release-local',
                    snapshotRepo: 'openmrsmaven-libs-snapshot-local',
                )

                rtMavenRun (
                    tool: 'MAVEN_HOME',
                    pom: 'pom.xml',
                    goals: 'clean install',
                    resolverId: 'openmrs-resolver-unique-id',
                    deployerId: 'openmrs-deployer-unique-id'
                    
                )

            }
        }
        stage ('publishing build info') {
            steps {
                rtPublishBuildInfo (
                    serverId: 'jfrog',
                )
                rtBuildInfo (
                    captureEnv: true,
                )
                rtDownload (
                    serverId: 'jfrog',
                //     specPath: '**/workspace/spec.json'
                )

                rtUpload (
                    serverId: 'jfrog',
                //     specPath: '**/workspace/spec.json'
                )

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
