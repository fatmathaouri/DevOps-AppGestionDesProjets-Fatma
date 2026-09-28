pipeline {
    agent any

    environment {
        DOCKERHUB_USER = 'fatmathaouri'
        BACKEND_IMAGE  = 'fatmathaouri/devops-app'
        FRONTEND_IMAGE = 'fatmathaouri/devops-front'
    }

    stages {

        stage('Checkout') {
            steps {
                echo 'Récupération du projet depuis GitHub'
            }
        }

        stage('Build Backend') {
            steps {
                sh '''
                    cd backend
                    chmod +x mvnw
                    ./mvnw clean package -DskipTests
                '''
            }
        }

        stage('Build Docker Backend') {
            steps {
                sh '''
                    docker build -t ${BACKEND_IMAGE}:latest ./backend
                '''
            }
        }

        stage('Build Docker Frontend') {
            steps {
                sh '''
                    docker build -t ${FRONTEND_IMAGE}:latest ./frontend
                '''
            }
        }

        stage('Login Docker Hub') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub',
                        usernameVariable: 'DOCKERHUB_USER',
                        passwordVariable: 'DOCKERHUB_TOKEN'
                    )
                ]) {
                    sh '''
                        echo "$DOCKERHUB_TOKEN" | docker login \
                            -u "$DOCKERHUB_USER" \
                            --password-stdin
                    '''
                }
            }
        }

        stage('Push Docker Images') {
            steps {
                sh '''
                    docker push ${BACKEND_IMAGE}:latest
                    docker push ${FRONTEND_IMAGE}:latest
                '''
            }
        }
    }

    post {
        success {
            echo 'CI/CD terminé avec succès !'
        }

        failure {
            echo 'Le pipeline a échoué.'
        }
    }
}
