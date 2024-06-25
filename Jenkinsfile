pipeline {
    agent any

    stages {
        stage('Build') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                
              sh node --version
              sh  npm --version
              sh  npm ci
              sh   npm run build
              sh   ls -la
            
            }
        }
    }
}
