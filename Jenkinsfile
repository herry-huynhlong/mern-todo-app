pipeline {
    agent any

    tools {
        nodejs 'NodeJS-22'
    }

    options {
        timestamps()
        disableConcurrentBuilds()
        skipDefaultCheckout(true)
    }

    environment {
        APP_DIR = 'mern-todo-app'
        CI = 'false'
    }

    stages {
        stage('Checkout GitHub') {
            steps {
                checkout scm
            }
        }

        stage('Check Environment') {
            steps {
                sh '''
                    node --version
                    npm --version
                '''
            }
        }

        stage('Check Backend') {
            steps {
                dir("${APP_DIR}/backend") {
                    sh '''
                        npm ci
                        find . -path './node_modules' -prune -o \
                          -name '*.js' -type f -exec node --check {} \\;
                    '''
                }
            }
        }

        stage('Check Frontend') {
            steps {
                dir("${APP_DIR}/frontend") {
                    sh '''
                        npm ci
                        npm run build
                    '''
                }
            }
        }
    }

    post {
        success {
            echo '================================'
            echo 'JENKINS CI SUCCESS!'
            echo 'BACKEND CHECK PASSED'
            echo 'FRONTEND BUILD PASSED'
            echo '================================'
        }

        failure {
            echo 'JENKINS CI FAILED!'
        }

        always {
            echo 'PIPELINE FINISHED'
        }
    }
}
