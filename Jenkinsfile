pipeline {
    agent any

    stages {
        stage('Build') {
            agent {
                docker {
                    image 'node:18' // เปลี่ยนเป็น Debian-based image
                    reuseNode true
                    // args '--dns 8.8.8.8 --dns 8.8.4.4' // กำหนด DNS server
                }
            }
            steps {
                sh '''
                    ls -la
                    node --version
                    npm --version
                    rm -rf node_modules # ลบ node_modules เพื่อหลีกเลี่ยงปัญหาสิทธิ์
                    npm ci
                    npm run build
                    ls -la
                '''
            }
        }

        stage('Test') {
            agent {
                docker {
                    image 'node:18' // ใช้ Debian-based image
                    reuseNode true
                    // args '--dns 8.8.8.8 --dns 8.8.4.4' // กำหนด DNS server
                }
            }
            steps {
                sh '''
                    test -f build/index.html
                    npm test
                '''
            }
            post {
                always {
                    junit 'test-results/junit.xml'
                }
            }
        }

        stage('Deploy') {
            agent {
                docker {
                    image 'node:18' // ใช้ Debian-based image
                    reuseNode true
                    // args '--dns 8.8.8.8 --dns 8.8.4.4' // กำหนด DNS server
                }
            }
            steps {
                sh '''
                    # ติดตั้ง glob เวอร์ชัน 9 ขึ้นไป
                    npm install glob@^9.0.0

                    # แสดงเวอร์ชันของ glob
                    echo "Glob version:"
                    npm list glob

                    # ติดตั้ง netlify-cli แบบ local
                    npm install netlify-cli

                    # แสดงเวอร์ชันของ netlify-cli
                    ./node_modules/.bin/netlify --version
                '''
            }
        }
    }
}
