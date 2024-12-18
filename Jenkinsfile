pipeline {
    agent any

    stages {
        stage('Build') {
            agent {
                docker {
                    image 'node:18' // ใช้ Debian-based image
                    reuseNode true
                }
            }
            steps {
                sh '''
                    node --version
                    npm --version
                    npm ci
                    npm run build
                '''
            }
        }

        stage('Test') {
            agent {
                docker {
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
            agent {
                docker {
                    image 'node:18'
                    reuseNode true
                }
            }
            steps {
                script {
                    retry(3) {
                        sh '''
                            npm install netlify-cli
                            node_modules/.bin/netlify --version
                        '''
                    }
                }
            }
        }
    }
}
