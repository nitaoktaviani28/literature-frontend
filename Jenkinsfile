pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "elilaura/literature-frontend"
        DOCKER_TAG = "v1.0.0"
        CONTAINER_NAME = "literature-frontend"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build App') {
            agent {
                docker {
                    image 'node:14-alpine'
                    args '-u root'
                }
            }
            steps {
                sh '''
                  npm install
                  npm run build
                '''
            }
        }

        stage('Docker Build') {
            steps {
                sh '''
                  docker build -t $DOCKER_IMAGE:$DOCKER_TAG .
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
                      echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                    '''
                }
            }
        }

        stage('Docker Push') {
            steps {
                sh '''
                  docker push $DOCKER_IMAGE:$DOCKER_TAG
                '''
            }
        }

        stage('Deploy Container') {
            steps {
                sh '''
                  docker rm -f $CONTAINER_NAME || true
                  docker run -d \
                    --name $CONTAINER_NAME \
                    -p 3000:3000 \
                    $DOCKER_IMAGE:$DOCKER_TAG
                '''
            }
        }
    }

    post {
        success {
            echo 'CI/CD berhasil: build → push → deploy 🚀'
        }
        failure {
            echo 'CI/CD gagal ❌'
        }
    }
}
