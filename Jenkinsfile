pipeline {
    agent any

    tools {
        nodejs 'node'
    }

    environment {
        BRANCH_NAME = "${env.BRANCH_NAME}"
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
                sh "docker rm -f ${IMAGE_NAME} || true"
                sh "docker run -d --name ${IMAGE_NAME} -p ${APP_PORT}:3000 ${IMAGE_NAME}:v1.0"
            }
        }
    }
}
