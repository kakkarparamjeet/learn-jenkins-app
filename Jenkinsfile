 pipeline {
    agent any
    
    environment {
        // Set the PATH to include the node_modules/.bin directory
        PATH = "${env.WORKSPACE}/node_modules/.bin:${env.PATH}"
     NETLIFY_SITE_ID = '9be7f22e-2eb7-4874-9664-0d6ba9ea5800'
     NETLIFY_AUTH_TOKEN = credentials('netlify-token')
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

     stage('Tests') {
      parallel {
      stage('Unit Test') {
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
            post {
       always {
        junit 'jest-results/junit.xml'
    
        publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'Playwright HTML Report', reportTitles: '', useWrapperFileDirectly: true])
    
   }
  }
           }
      
       stage('local E2E') {
            agent {
                      docker {
                        args '-u root:root'
                          image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                       
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
            npx playwright test --reporter=html
            
            #
             '''
            }
        post {
     always {
  
    
          publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'Playwright local HTML Report', reportTitles: '', useWrapperFileDirectly: true])
    
          }
       }

           }

stage('Deploy') {
agent {
                docker {
                   args '-u root:root'
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    npm install netlify-cli
                    node_modules/.bin/netlify --version
                    echo "Deploying to production. Site ID: $NETLIFY_SITE_ID"
                    node_modules/.bin/netlify status
                    # we need to mention which folder to deploy and deploy to production
                    node_modules/.bin/netlify deploy --dir=build --prod
                '''
            }
  }

stage('prod E2E') {
            agent {
                      docker {
                        args '-u root:root'
                          image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                       
                          reuseNode true
                      }
                  }
            steps {
            sh '''
            
            npx playwright test --reporter=html
            
            #
             '''
            }
        post {
     always {
  
    
          publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'Playwright E2E HTML Report', reportTitles: '', useWrapperFileDirectly: true])
    
          }
       }

           }




           
             
            }
           }
           
     
    }
  
  }
