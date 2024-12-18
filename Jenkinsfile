pipeline {
    agent any
 
    stages {
        stage('Build') {
            agent{
                docker{
                    image 'node:18'
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

        stage('Test') {
            agent{
                docker{
                    image 'node:18'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    test -f build/index.html
                    npm test
                '''
            }
        }

        stage('Deploy') {
            agent{
                docker{
                    image 'node:18'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    npm install glob@^9.0.0
                    echo "Glob version:"
                    npm list glob
                    
                    npm install netlify-cli -g
                    netlify --version
                '''
            }
        }
    }
}