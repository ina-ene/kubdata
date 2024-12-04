
pipeline {
    agent {
        label 'nodejs'
    }
    environment {
        DOCKER_IMAGE = 'harbor.bahur:443/nodejs/nodejs-app:latest'
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
                    // Build images as defined in docker-compose.yml
                    sh 'docker-compose -f docker-compose.yaml build'
                }
            }
        }

        stage('Push to Harbor') {
            steps {
                script {
                    // Log in to Harbor
                    docker.withRegistry('https://harbor.bahur:443', 'jenkins-harbor-robot') {
                        // Assuming your service name in docker-compose.yml is `nodejs-app`
                        def serviceImageId = sh(script: "docker-compose -f docker-compose.yaml images -q stories", returnStdout: true).trim()

                        // Tag the image appropriately if necessary
                        // Assuming docker-compose.yml sets this image name and tag already, adjust if needed
                        sh "docker tag ${serviceImageId} ${DOCKER_IMAGE}"

                        // Push the tagged image to the registry
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
