pipeline {
    agent any
    environment {
        // Set the PATH to include the node_modules/.bin directory
        PATH = "${env.WORKSPACE}/node_modules/.bin:${env.PATH}"
    }

    stages {
     
     
        stage('Build') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
              sh '''
                 ls -la
                 node --version
                 npm --version
                 chown -R 114:122 "/.npm"
               npm ci
                 npm run build
                 ls -la
                 
             '''
            }
        }
    }
}
