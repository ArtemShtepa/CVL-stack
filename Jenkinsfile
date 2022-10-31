pipeline {
    agent any

    stages {
        stage('Second branch') {
            steps {
                echo 'SECOND'
            }
        }
        stage('Molecute Test') {
            steps {
                sh 'molecule test'
            }
        }
    }
}
