pipeline {
    agent any

    stages {
        

        stage('clean') {
            steps {
                sh 'npm run clean'
            }
        }

        stage('Build') {
            steps {
                sh 'npm run build'
            }
        }
    }
}
