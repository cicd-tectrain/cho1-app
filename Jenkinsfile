pipeline {
    agent any

    environment {
        INTEGRATION_BRANCH = 'integration'
    }
    stages {
        stage('Build'){
            // limit branches
            when  {
                branch 'feature/*'
                beforeAgent true
            }


            //Docker Agent
            agent {
                docker {
                    image 'gradle:7.5.1-jdk17-focal'
                }
            }


            steps {
                echo 'Build Feature...'
                sh 'gradle clean build -x test'
                sh 'ls -la build/libs'
            }
        }

        stage('Test'){

            when  {
                branch 'feature/*'
                beforeAgent true
            }

            //Docker Agent
            agent {
                docker {
                    image 'gradle:7.5.1-jdk17-focal'
                }
            }
            steps {
                echo 'Test Feature...'
                sh 'gradle test'
                //Junit XML Reports
                sh 'ls -la build/test-results/test'
                sh 'ls -la build/reports/tests'
            }
            // Post Build Actions
            post {
                always {
                    // Junit results archivieren
                    junit 'build/test-results/test/*.xml'
                }

                success {
                    publishHTML target: [
                        allowMissing: true,
                        alwaysLinkToLastBuild: false,
                        keepAll: true,
                        reportDir: 'build/reports/tests/test',
                        reportFiles: 'index.html',
                        reportName: 'Test-Report'
                    ]
                }

            }
        }

         stage('Integrate'){

             when  {
                 branch 'feature/*'
                 beforeAgent true
             }
             steps {
                 echo "Integrating..."
                 sh 'git --version'
                 sh 'git branch -a'
                 sh 'git checkout ${INTEGRATION_BRANCH}'
                 sh 'git pull'
                 // FIX ME
                 sh 'git merge --no-ff --no-edit remotes/origin/${BRANCH_NAME}'
                 // Pushen
                 withCredentials([gitUsernamePassword(credentialsId: 'github', gitToolName: 'Default')]) {
                     sh 'git push origin ${INTEGRATION_BRANCH}'
                 }
             }
         }
//===================================== INTEGRATION=======================
        stage('Build Integration'){
            // limit branches
            when  {
                branch "${INTEGRATION_BRANCH}"
                beforeAgent true
            }


            //Docker Agent
            agent {
                docker {
                    image 'gradle:7.5.1-jdk17-focal'
                }
            }


            steps {
                echo 'Build Integration...'
                sh 'gradle clean build -x test'
                sh 'ls -la build/libs'

            }

            // Stash if stage successful
            post {
                success {
                    stash name: 'integration_build', includes: 'build/'
                }
            }
        }

        stage('Test Integration'){

            when  {
                branch "${INTEGRATION_BRANCH}"
                beforeAgent true
            }

            //Docker Agent
            agent {
                docker {
                    image 'gradle:7.5.1-jdk17-focal'
                }
            }
            steps {
                echo 'Test Integration...'
                sh 'gradle test'
                //Junit XML Reports
                sh 'ls -la build/test-results/test'
                sh 'ls -la build/reports/tests'
            }
            // Post Build Actions
            post {
                always {
                    // Junit results archivieren
                    junit 'build/test-results/test/*.xml'
                }

                success {
                    publishHTML target: [
                        allowMissing: true,
                        alwaysLinkToLastBuild: false,
                        keepAll: true,
                        reportDir: 'build/reports/tests/test',
                        reportFiles: 'index.html',
                        reportName: 'Test-Report'
                    ]
                }

            }
        }

        stage("PUblish Artifacts") {
            when  {
                branch "${INTEGRATION_BRANCH}"
                beforeAgent true
            }

            steps {
                // Unstash
                unstash 'integration_build'

                // Publish artifact in nexus
                nexusArtifactUploader artifacts: [
                [
                    artifactId: at.tectrain.app,
                    classifier: '',
                    file: 'build/libs/app-0.0.1-SNAPSHOT.jar'
                    type: 'jar'
                ],
                credentialsId: 'nexus_credentials',
                groupId: '',
                nexusUrl: 'nexus:8081/repository/maven-snapshots',
                protocol: 'http',
                repository: '',
                version: '0.0.1-SNAPSHOT'
                ]
            }


        }

        stage("Deploy integration branch") {
            when  {
                branch "${INTEGRATION_BRANCH}"
                beforeAgent true
            }

            steps {
                echo "Deployment..."
            }
            // docker image bauen und starten (und archivieren)

            // Env für Nexus Credentials

            // Image bauen -> Dockerfile
            //sh 'docker build -t XXX .'
            // Image taggen

            //sh 'docker login nexus:8081'

            // Image pushen

        }

    }

}
