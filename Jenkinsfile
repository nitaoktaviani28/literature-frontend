pipeline {
    agent any

    environment {
        APP_NAME = "literature-frontend"
        DOCKER_IMAGE = "literature-frontend:latest"
        CONTAINER_NAME = "literature-frontend"
        PORT = "3000"
    }

    stages {

        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Build Frontend') {
            steps {
                sh 'npm run build'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                docker build -t $DOCKER_IMAGE .
                '''
            }
        }

        stage('Deploy Docker Container') {
            steps {
                sh '''
                docker rm -f $CONTAINER_NAME || true
                docker run -d \
                  --name $CONTAINER_NAME \
                  -p $PORT:80 \
                  $DOCKER_IMAGE
                '''
            }
        }
    }

    post {
        success {
            echo 'CI/CD pipeline berhasil'
        }
        failure {
            echo 'CI/CD pipeline gagal'
        }
    }
}
