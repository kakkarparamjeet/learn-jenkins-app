pipeline {
    agent any
    environment {
        // Set the PATH to include the node_modules/.bin directory
        PATH = "${env.WORKSPACE}/node_modules/.bin:${env.PATH}"
    }

    stages {
     
     stage('Install Dependencies') {
            steps {
                sh 'node -v'
                
                sh 'npm install'
                sh 'npm -v'
            }
        }
        stage('Build') {
          
            steps {
              sh '''
                 ls -la
                 node --version
                 npm --version
                 
               npm ci
                 npm run build
                 ls -la
                 
             '''
            }
        }
    }
}
