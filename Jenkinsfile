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
        CI = 'false'
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
                echo '========== CHECK ENVIRONMENT =========='

                sh '''
                    node --version
                    npm --version
                    pm2 --version
                '''
            }
        }

        stage('Install Backend Dependencies') {
            steps {
                echo '========== INSTALL BACKEND DEPENDENCIES =========='

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
                echo '========== INSTALL FRONTEND DEPENDENCIES =========='

                dir("${APP_DIR}/frontend") {
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

        stage('Build Frontend') {
            steps {
                echo '========== BUILD REACT FRONTEND =========='

                dir("${APP_DIR}/frontend") {
                    sh '''
                        npm run build
                    '''
                }
            }
        }

        stage('Deploy Backend') {
            steps {
                echo '========== DEPLOY NODEJS BACKEND =========='

                dir("${APP_DIR}/backend") {
                    sh '''
                        if pm2 describe "$APP_NAME" > /dev/null 2>&1; then
                            echo "Restarting backend..."
                            pm2 restart "$APP_NAME" --update-env
                        else
                            echo "Starting backend..."
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
            echo 'JENKINS PIPELINE SUCCESS!'
            echo 'BACKEND DEPLOYED SUCCESSFULLY!'
            echo '========================================'
        }

        failure {
            echo '========================================'
            echo 'JENKINS PIPELINE FAILE!'
            echo 'PLEASE CHECK CONSOLE OUTPUT'
            echo '========================================'
        }

        always {
            echo '========== PIPELINE FINISHED =========='
        }
    }
}
