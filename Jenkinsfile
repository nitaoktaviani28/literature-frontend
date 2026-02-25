pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "haechanlovelove/literature-frontend"
        DOCKER_TAG   = "v1.0.0"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build React App') {
            steps {
                sh '''
                  node -v || echo "Node belum terinstall"
                  npm install
                  CI=false npm run build
                '''
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                  docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} .
                '''
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh '''
                      echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                    '''
                }
            }
        }

        stage('Docker Push') {
            steps {
                sh '''
                  docker push ${DOCKER_IMAGE}:${DOCKER_TAG}
                '''
            }
        }

        stage('Deploy with Docker Compose') {
            steps {
                sh '''
                  docker compose down || true
                  docker compose pull
                  docker compose up -d
                '''
            }
        }
    }

    post {
        success {
            echo '✅ CI/CD berhasil (Docker Compose)'
        }
        failure {
            echo '❌ CI/CD gagal'
        }
    }
}
