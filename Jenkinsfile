pipeline {

    agent any

    environment {
        IMAGE_NAME = "jenkins-practice"
        CONTAINER_NAME = "jenkins-practice-app"
        APP_PORT = "3000"
    }

    stages {

        stage('Checkout') {
            steps {
                echo '========== CHECKOUT =========='
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                echo '========== INSTALL DEPENDENCIES =========='
                sh 'npm install'
            }
        }

        stage('Test') {
            steps {
                echo '========== RUNNING TEST =========='
                sh 'npm test'
            }
        }

        stage('Docker Build') {
            steps {
                echo '========== BUILDING DOCKER IMAGE =========='
                sh 'docker build -t ${IMAGE_NAME}:${BUILD_NUMBER} .'
                sh 'docker tag ${IMAGE_NAME}:${BUILD_NUMBER} ${IMAGE_NAME}:latest'
            }
        }

        stage('Deploy') {
            steps {
                echo '========== DEPLOYING APPLICATION =========='

                sh '''
                    docker stop ${CONTAINER_NAME} || true
                    docker rm ${CONTAINER_NAME} || true

                    docker run -d \
                        --name ${CONTAINER_NAME} \
                        -p ${APP_PORT}:3000 \
                        ${IMAGE_NAME}:${BUILD_NUMBER}
                '''
            }
        }

        stage('Health Check') {
            steps {
                echo '========== HEALTH CHECK =========='

                sh '''
                    sleep 5
                    curl -f http://localhost:${APP_PORT}
                '''
            }
        }
    }

    post {

        success {
            echo '======================================'
            echo ' CI/CD PIPELINE SUCCESSFUL!'
            echo ' Application deployed successfully.'
            echo '======================================'
        }

        failure {
            echo '======================================'
            echo ' CI/CD PIPELINE FAILED!'
            echo ' Check Console Output.'
            echo '======================================'
        }
    }
}
