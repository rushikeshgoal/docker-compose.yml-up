pipeline {
    agent any

    environment {
        DOCKER_HUB_CREDENTIALS = 'dockerhub-cred'   // Jenkins credentials ID for Docker Hub
        DOCKERHUB_USER = 'rushikeshdoc'             // your Docker Hub username
        BACKEND_IMAGE = 'backend:githubactionb'
        FRONTEND_IMAGE = 'frontend:hrmstej04'
    }

    stages {

        stage('Checkout') {
            steps {
                echo '📦 Cloning source code...'
                git branch: 'main', url: 'https://github.com/rushikeshgoal/docker-compose.yml-up.git'  // change this to your actual repo
            }
        }

        stage('Build Backend Docker Image') {
            steps {
                script {
                    echo '⚙️ Building Spring Boot backend image...'
                    sh 'docker build -t ${DOCKERHUB_USER}/${BACKEND_IMAGE} ./backend'
                }
            }
        }

        stage('Build Frontend Docker Image') {
            steps {
                script {
                    echo '⚙️ Building Angular frontend image...'
                    sh 'docker build -t ${DOCKERHUB_USER}/${FRONTEND_IMAGE} ./frontend'
                }
            }
        }

        stage('Push Images to Docker Hub') {
            steps {
                script {
                    echo '🚀 Pushing images to Docker Hub...'
                    withCredentials([usernamePassword(credentialsId: "${DOCKER_HUB_CREDENTIALS}", usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                        sh '''
                            echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                            docker push ${DOCKERHUB_USER}/${BACKEND_IMAGE}
                            docker push ${DOCKERHUB_USER}/${FRONTEND_IMAGE}
                        '''
                    }
                }
            }
        }

        stage('Deploy using Docker Compose') {
            steps {
                script {
                    echo '🐳 Deploying containers using docker-compose...'
                    sh '''
                        docker compose down
                        docker compose up -d
                    '''
                }
            }
        }
    }

    post {
        success {
            echo '✅ Deployment successful! Your HRMS app is live.'
        }
        failure {
            echo '❌ Deployment failed. Check console output for errors.'
        }
    }
}

