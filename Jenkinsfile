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
        APP_NAME = 'mern-todo-backend'
        APP_DIR = 'mern-todo-app'
    }

    stages {

        stage('Checkout GitHub') {
            steps {
                echo '========== CHECKOUT SOURCE =========='
                checkout scm
            }
        }

        stage('Check Environment') {
            steps {
                echo '========== CHECK NODEJS =========='

                sh '''
                    node --version
                    npm --version
                '''
            }
        }

        stage('Install Backend Dependencies') {
            steps {
                echo '========== INSTALL BACKEND =========='

                dir("${APP_DIR}/backend") {
                    sh '''
                        if [ -f package-lock.json ]; then
                            npm ci
                        else
                            npm install
                        fi
                    '''
                }
            }
        }

        stage('Install Frontend Dependencies') {
            steps {
                echo '========== INSTALL FRONTEND =========='

                dir("${APP_DIR}/frontend") {
                    sh '''
                        npm ci
                    '''
                }
            }
        }

        stage('Build Frontend') {
            steps {
                echo '========== BUILD REACT =========='

                dir("${APP_DIR}/frontend") {
                    sh '''
                        npm run build
                    '''
                }
            }
        }

        stage('Deploy Backend') {
            steps {
                echo '========== DEPLOY BACKEND =========='

                dir("${APP_DIR}/backend") {
                    sh '''
                        if ! command -v pm2 >/dev/null 2>&1; then
                            npm install -g pm2
                        fi

                        if pm2 describe "$APP_NAME" >/dev/null 2>&1; then
                            pm2 restart "$APP_NAME" --update-env
                        else
                            pm2 start server.js --name "$APP_NAME"
                        fi

                        pm2 save
                    '''
                }
            }
        }

        stage('Check Deployment') {
            steps {
                echo '========== CHECK DEPLOYMENT =========='

                sh '''
                    pm2 list
                '''
            }
        }
    }

    post {
        success {
            echo '========================================'
            echo 'BUILD AND DEPLOY SUCCESS!'
            echo '========================================'
        }

        failure {
            echo '========================================'
            echo 'PIPELINE FAILED!'
            echo 'CHECK CONSOLE OUTPUT'
            echo '========================================'
        }
    }
}