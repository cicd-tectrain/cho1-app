pipeline {
    agent any

    stages {
        stage('Build'){
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
