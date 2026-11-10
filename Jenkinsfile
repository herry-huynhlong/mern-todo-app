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

        stage('Check Backend Syntax') {
            steps {
                dir('mern-todo-app/backend') {
                    sh '''
                        find . -path './node_modules' -prune -o \
                          -name '*.js' -type f -exec node --check {} \\;
                    '''
                }
            }
        }

        stage('Check Frontend Structure') {
            steps {
                dir('mern-todo-app/frontend') {
                    sh '''
                        test -f package.json
                        test -f src/App.js
                        test -f src/index.js

                        echo "Frontend source structure OK"
                    '''
                }
            }
        }

        stage('Check Docker Configuration') {
            steps {
                sh '''
                    test -f mern-todo-app/docker-compose.yml
                    test -f mern-todo-app/backend/Dockerfile
                    test -f mern-todo-app/frontend/Dockerfile

                    echo "Docker configuration files OK"
                '''
            }
        }
    }

    post {
        success {
            echo 'JENKINS CI SUCCESS!'
        }

        failure {
            echo 'JENKINS CI FAILED!'
        }
    }
}
