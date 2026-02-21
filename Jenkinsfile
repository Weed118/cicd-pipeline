pipeline {
    agent any

    tools {
        nodejs 'node'
    }

    environment {
        APP_PORT = "${env.BRANCH_NAME == 'main' ? '3000' : '3001'}"
        IMAGE_NAME = "${env.BRANCH_NAME == 'main' ? 'nodemain' : 'nodedev'}"
    }

    stages {
        stage('Checkout SCM') {
            steps {
                checkout scm
            }
        }

        stage('Tool Install') {
            steps {
                sh 'npm install'
            }
        }

        stage('Build') {
            steps {
                sh 'npm run build'
            }
        }

        stage('Test') {
            steps {
                sh 'npm test -- --watchAll=false'
            }
        }

        stage('Docker build') {
            steps {
                sh "docker build -t ${IMAGE_NAME}:v1.0 ."
            }
        }

        stage('Deploy') {
            steps {
                script {
                    if (sh(script: "docker ps -a -q -f name=${IMAGE_NAME}", returnStdout: true).trim()) {
                        sh "docker stop ${IMAGE_NAME}"
                        sh "docker rm ${IMAGE_NAME}"
                    } else {
                        echo "No running containers found for ${IMAGE_NAME}."
                    }
                    sh "docker run -d --expose ${APP_PORT} --name ${IMAGE_NAME} -p ${APP_PORT}:3000 ${IMAGE_NAME}:v1.0"
                }
            }
        }
    }
}
