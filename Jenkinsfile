pipeline {
    agent any

    stages {
        stage('Main branch') {
            steps {
                echo 'MAIN'
            }
        }
        stage('Molecute Test') {
            steps {
                sh 'molecule test'
            }
        }
    }
}
