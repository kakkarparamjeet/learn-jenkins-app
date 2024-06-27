 
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
                 
                 npm install
                 npm run build
                 ls -la
                 
             '''
            }

         
        }
     stage('Test') {
      agent {
                docker {
                  args '-u root:root'
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
      steps {
      sh '''
       # First check build directory exist and in that file index.html exist.
        test -f build/index.html
       npm test
       '''
      }
     }
    }
  post {
   always (
    junit 'test-results/junit.xml'
   )
  }
  }
