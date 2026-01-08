pipeline {
    agent any

    environment {
        MSSV = '23120282' 
        DOCKER_IMAGE = "${MSSV}"
        CONTAINER_NAME = "cicd_app_${MSSV}"
        DOCKER_CREDENTIALS_ID = 'docker-hub-credentials'
    }

    stages {
        stage('Checkout') {
            steps {
                echo 'Checking out code...'
                checkout scm
            }
        }

        stage('Build Image') {
            steps {
                script {
                    echo 'Building Docker image...'
                    docker.build("${DOCKER_IMAGE}:latest")
                }
            }
        }

        stage('Push to DockerHub') {
            steps {
                script {
                    echo 'Pushing to Docker Hub...'
                    docker.withRegistry('', DOCKER_CREDENTIALS_ID) {
                        docker.image("${DOCKER_IMAGE}:latest").push()
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    echo 'Deploying application...'
                    sh "docker rm -f ${CONTAINER_NAME} || true"
                    sh """
                        docker run -d \
                        --name ${CONTAINER_NAME} \
                        -p 8080:80 \
                        ${DOCKER_IMAGE}:latest
                    """
                }
            }
        }
    }
}
