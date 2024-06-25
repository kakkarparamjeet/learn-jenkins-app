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
                
              bat  'node --version'
              bat  'npm --version'
              bat  'npm ci'
              bat   'npm run build'
              bat   'ls -la'
            
            }
        }
    }
}
