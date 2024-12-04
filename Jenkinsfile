
pipeline {
    agent {
        label 'nodejs'
    }
    environment {
        DOCKER_IMAGE = 'harbor.bahur:443/node-app/nodejs-app:1'
    }

    stages {
        stage('Checkout') {
            steps {
                // Checkout code from the repository
                checkout scm
            }
        }

        stage('Build Docker Image with Docker Compose') {
            steps {
                script {
                    // Set environment variable for docker-compose
                    withEnv(["DOCKER_IMAGE=${env.DOCKER_IMAGE}"]) {
                        // Build images as defined in docker-compose.yml and use the DOCKER_IMAGE as the tag
                        sh 'docker-compose -f docker-compose.yaml build'
                    }
                }
            }
        }

        stage('Push to Harbor') {
            steps {
                script {
                    // Log in to Harbor
                    docker.withRegistry('https://harbor.bahur:443', 'jenkins-harbor-robot') {
                        // Push the image that's already tagged during the build
                        sh "docker push ${DOCKER_IMAGE}"
                    }
                }
            }
        }
    }
    post {
        always {
            // Clean up workspace and any leftover Docker artifacts
            cleanWs()
        }
    }
}
