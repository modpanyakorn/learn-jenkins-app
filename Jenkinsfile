pipeline {
    agent any

    stages {
        stage('Build') {
            agent {
                docker {
                    image 'node:18' // ใช้ Debian-based image
                    reuseNode true
                    args '-u 0:0' // รันเป็น root user
                }
            }
            steps {
                sh '''
                    # ติดตั้ง Python3, make, g++
                    apt-get update && apt-get install -y python3 make g++

                    # ตรวจสอบการติดตั้ง Python
                    python3 --version

                    # ตรวจสอบการติดตั้ง Node และ npm
                    node --version
                    npm --version

                    # ติดตั้ง dependencies และ build
                    npm ci
                    npm run build
                '''
            }
        }

        stage('Test') {
            agent {
                docker {
                    image 'node:18' // ใช้ Debian-based image
                    reuseNode true
                    args '-u 0:0' // รันเป็น root user
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
                    args '-u 0:0'
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
