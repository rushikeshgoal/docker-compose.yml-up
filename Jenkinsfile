pipeline {
    agent any

    environment {
        DOCKERHUB_USER = 'rushikeshdoc'
        BACKEND_IMAGE = 'backend:githubactionb'
        FRONTEND_IMAGE = 'frontend:hrmstej04'
        COMPOSE_FILE = 'docker-compose.yml'
    }

    stages {
        stage('Checkout') {
            steps {
                echo "📦 Cloning source code..."
                checkout scm
            }
        }

        stage('Build Backend Docker Image') {
            steps {
                echo "⚙️ Building Spring Boot backend image..."
                sh "docker build -t ${DOCKERHUB_USER}/${BACKEND_IMAGE} ./backend"
            }
        }

        stage('Build Frontend Docker Image') {
            steps {
                echo "⚙️ Building Angular frontend image..."
                sh "docker build -t ${DOCKERHUB_USER}/${FRONTEND_IMAGE} ./frontend"
            }
        }

        stage('Push Images to Docker Hub') {
            steps {
                echo "🚀 Pushing images to Docker Hub..."
                withCredentials([usernamePassword(credentialsId: 'dockerhub-cred', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                    sh "echo $PASSWORD | docker login -u $USERNAME --password-stdin"
                    sh "docker push ${DOCKERHUB_USER}/${BACKEND_IMAGE}"
                    sh "docker push ${DOCKERHUB_USER}/${FRONTEND_IMAGE}"
                }
            }
        }

        stage('Deploy with Docker Compose') {
            steps {
                echo "📂 Deploying services with Docker Compose..."
                sh "docker-compose -f ${COMPOSE_FILE} up -d"
            }
        }
    }

    post {
        success {
            echo "✅ Deployment successful!"
        }
        failure {
            echo "❌ Deployment failed. Check console output for errors."
        }
    }
}

