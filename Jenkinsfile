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
            }
        }

        stage('Test'){
            steps {
                echo 'Test Feature...'
            }
        }

         stage('Integrate'){
             steps {
                 echo 'Integrate Feature...'
             }
         }

    }

}
