 
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
                  args '-u root:root'
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
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
