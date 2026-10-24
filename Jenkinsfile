
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

        stage("E2E") {
            agent {
                docker {
                    image 'mcr.microsoft.com/playwright:v1.64.0-noble'
                    reuseNode true
                }
            }
            steps {
                sh '''
                npx --yes serve -s build -l 3000 &
                serve -s build
                npx playwright test
                '''
            }
        }

        stage("Test") {
            agent {
                docker {
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
        }
    }

    post {
        always {
            junit testResults: '**/junit*.xml', allowEmptyResults: true
        }
    }
}
