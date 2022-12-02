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
            }
        }

         stage('Integrate'){
             steps {
                 echo 'Integrate Feature...'
             }
         }

    }

}
