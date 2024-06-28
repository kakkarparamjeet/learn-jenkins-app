 
 pipeline {
    agent any
    environment {
        // Set the PATH to include the node_modules/.bin directory
        PATH = "${env.WORKSPACE}/node_modules/.bin:${env.PATH}"
    }

    stages {

     /*
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
        */
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
 stage('E2E') {
      agent {
                docker {
                  args '-u root:root'
                    image 'mcr.microsoft.com/playwright:v1.45.0-jammy'
                    reuseNode true
                }
            }
      steps {
      sh '''
      # startting the application
      # first we install serve and start globally
      
      # npm install -g serve
      # but we need  to start locally
      npm install serve
      # start a web server in build diretory
      # serve -s build
      # & will run the server in background
      node_modules/.bin/serve -s build &
      sleep 15
      npx playwright test
      
      #
       '''
      }
     }
     
    }
  post {
   always {
    junit 'jest-results/junit.xml'
    
   }
  }
  }
