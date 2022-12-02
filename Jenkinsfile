pipeline {
    agent any


    stages {
        stage('Build'){

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
             steps {
                 echo "Integrating..."
                 sh 'git --version'
                 sh 'git branch -a'
                 sh 'git checkout integration'
                 sh 'git pull'
                 // FIX ME
                 sh 'git merge --no-ff --no-edit remotes/origin/feature/1'
                 // Pushen
                 withCredentials([gitUsernamePassword(credentialsId: 'github_pat', gitToolName: 'Default')]) {
                     sh 'git push origin integration'
              }
         }

    }

}
