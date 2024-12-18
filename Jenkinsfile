pipeline {
    agent any

    stages {
        stage('Build') {
            agent {
                docker {
                    image 'node:18' // ใช้ Debian-based image
                    reuseNode true
                    args '-u 0:0 --dns 8.8.8.8 --dns 8.8.4.4' // รันเป็น root user และกำหนด DNS
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
                    args '-u 0:0 --dns 8.8.8.8 --dns 8.8.4.4' // รันเป็น root user และกำหนด DNS
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
                    image 'node:18' // ใช้ Debian-based image
                    reuseNode true
                    args '-u 0:0 --dns 8.8.8.8 --dns 8.8.4.4' // รันเป็น root user และกำหนด DNS
                }
            }
            steps {
                script {
                    retry(3) { // ลองติดตั้งซ้ำได้ 3 ครั้ง
                        sh '''
                            # ตรวจสอบการเชื่อมต่อเครือข่าย
                            echo "Checking network connectivity..."
                            ping -c 2 registry.npmjs.org || echo "Ping failed"
                            curl -I https://registry.npmjs.org || echo "Curl failed"

                            # ติดตั้ง netlify-cli
                            npm install netlify-cli
                            node_modules/.bin/netlify --version
                        '''
                    }
                }
            }
        }
    }
}
