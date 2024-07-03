

pipeline {
    agent any

    environment {
        NETLIFY_SITE_ID = '9be7f22e-2eb7-4874-9664-0d6ba9ea5800'
        NETLIFY_AUTH_TOKEN = credentials('netlify-token')
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

        stage('Tests') {
            parallel {
                stage('Unit tests') {
                    agent {
                        docker {
                        
                            args '-u root:root'
                            image 'node:18-alpine'
                            reuseNode true
                        }
                    }

                    steps {
                        sh '''
                            #test -f build/index.html
                            npm test
                        '''
                    }
                    post {
                        always {
                            junit 'jest-results/junit.xml'
                        }
                    }
                }

                stage('E2E') {
                    agent {
                        docker {
                            args '-u root:root'
                            image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                            reuseNode true
                        }
                    }

                    steps {
                        sh '''
                            npm install serve
                            node_modules/.bin/serve -s build &
                            sleep 10
                            npx playwright test  --reporter=html
                        '''
                    }

                    post {
                        always {
                            publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'Playwright Local', reportTitles: '', useWrapperFileDirectly: true])
                        }
                    }
                }
            }
        }

        stage('Deploy staging') {
            agent {
                docker {
                    args '-u root:root'
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    #   installing node-jq also
                    npm install netlify-cli node-jq
                    node_modules/.bin/netlify --version
                    echo "Deploying to staging. Site ID: $NETLIFY_SITE_ID"
                    node_modules/.bin/netlify status
                    #  Removeing --prod means it create review branch
                    #  redirecting the output to json file
                    node_modules/.bin/netlify deploy --dir=build --json > deploy-output.json
                    #  using jq search the text in file deploy-output.json
                    # move below line to script and we commneted below line
                   # node_modules/.bin/node-jq -r '.deploy_url' deploy-output.json
                    '''
                scripts {
                    env.STAGING_URL = sh(script: "node_modules/.bin/node-jq -r '.deploy_url' deploy-output.json", returnStdout: true)
                }
            }
        }
       /* we will add Staging test
       */
        stage('Staging E2E') {
            agent {
                docker {
                    args '-u root:root'
                    image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                    reuseNode true
                }
            }

            environment {
                /*
                # we don t know the url till now
                */
                CI_ENVIRONMENT_URL = "${env.STAGING_URL}"
            }

            steps {
                sh '''
                    npx playwright test  --reporter=html
                '''
            }

            post {
                always {
                    publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'Staging E2E', reportTitles: '', useWrapperFileDirectly: true])
                }
            }
        }
        #
        stage('approval') {
            
           
            steps {
                /*
                with time limit 1 minute if not replied then abort pipeline
                */
                timeout(1) {
input message: 'configrm', ok: 'Yes Please go ahead with'
                          }
                
            }
        }
        stage('Deploy prod') {
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
                    node_modules/.bin/netlify deploy --dir=build --prod
                '''
            }
        }
        stage('Prod E2E') {
            agent {
                docker {
                    args '-u root:root'
                    image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                    reuseNode true
                }
            }

            environment {
                CI_ENVIRONMENT_URL = 'https://lucent-mandazi-2221ef.netlify.app'
            }

            steps {
                sh '''
                    npx playwright test  --reporter=html
                '''
            }

            post {
                always {
                    publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'prod E2E', reportTitles: '', useWrapperFileDirectly: true])
                }
            }
        }
    }
}

