pipeline {
    agent any

    stages {
        stage('Get CVL roles') {
            steps {
                git branch: 'main', credentialsId: 'sa-github', url: 'git@github.com:ArtemShtepa/CVL-stack.git'
            }
        }
        stage('Molecute Test') {
            steps {
                sh 'molecule test'
            }
        }
    }
}
