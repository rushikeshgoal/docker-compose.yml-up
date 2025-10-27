pipeline {
    agent any

    environment {
        DOCKER_HUB_CREDENTIALS = 'dockerhub-cred'     // Jenkins credential ID
        DOCKERHUB_USER = 'rushikeshgoal'              // your DockerHub username
        BACKEND_IMAGE = 'rushikeshgoal/backend:githubactionb'
        FRONTEND_IMAGE = 'rushikeshgoal/frontend:hrmstej04'
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Backend Image') {
            steps {
                dir('backend') {
                    script {
                        sh 'docker build -t $BACKEND_IMAGE .'
                    }
                }
            }
        }

        stage('Build Frontend Image') {
            steps {
                dir('frontend') {
                    script {
                        sh 'docker build -t $FRONTEND_IMAGE .'
                    }
                }
            }
        }

        stage('Login to DockerHub') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: "$DOCKER_HUB_CREDENTIALS",
                                                     usernameVariable: 'USERNAME',
                                                     passwordVariable: 'PASSWORD')]) {
                        sh 'echo $PASSWORD | docker login -u $USERNAME --password-stdin'
                    }
                }
            }
        }

        stage('Push Images to DockerHub') {
            steps {
                script {
                    sh '''
                        docker push $BACKEND_IMAGE
                        docker push $FRONTEND_IMAGE
                    '''
                }
            }
        }

        stage('Deploy with Docker Compose') {
            steps {
                script {
                    sh 'docker-compose down || true'
                    sh 'docker-compose pull'
                    sh 'docker-compose up -d'
                }
            }
        }
    }

    post {
        always {
            echo "✅ Pipeline completed."
            sh 'docker ps -a'
        }
    }
}

