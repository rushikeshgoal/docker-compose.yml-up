pipeline {
    agent any

    environment {
        DOCKER_HUB_CREDENTIALS = 'dockerhub-cred'
        DOCKERHUB_USER = 'rushikeshdoc'
        BACKEND_IMAGE = 'backend:githubactionb'
        FRONTEND_IMAGE = 'frontend:hrmstej04'
    }

    stages {
        stage('Checkout') {
            steps {
                echo '🔹 Checking out code from GitHub...'
                checkout scm
            }
        }

        stage('Build Backend Docker Image') {
            steps {
                script {
                    sh '''
                    cd Backend_hrms
                    docker build -t ${DOCKERHUB_USER}/${BACKEND_IMAGE} .
                    '''
                }
            }
        }

        stage('Build Frontend Docker Image') {
            steps {
                script {
                    sh '''
                    cd frontend
                    docker build -t ${DOCKERHUB_USER}/${FRONTEND_IMAGE} .
                    '''
                }
            }
        }

        stage('Push Images to DockerHub') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: "${DOCKER_HUB_CREDENTIALS}", usernameVariable: "USER", passwordVariable: "PASS")]) {
                        sh '''
                        echo "$PASS" | docker login -u "$USER" --password-stdin
                        docker push ${DOCKERHUB_USER}/${BACKEND_IMAGE}
                        docker push ${DOCKERHUB_USER}/${FRONTEND_IMAGE}
                        docker logout
                        '''
                    }
                }
            }
        }

        stage('Deploy Using Docker Compose') {
            steps {
                script {
                    sh '''
                    docker-compose down
                    docker-compose up -d
                    '''
                }
            }
        }
    }

    post {
        success {
            echo '✅ Deployment successful!'
        }
        failure {
            echo '❌ Deployment failed! Check Jenkins logs.'
        }
    }
}

