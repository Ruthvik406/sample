piipeline {
    agent any

    environment {
        DOCKER_HUB_CREDENTIALS = credentials('docker-hub-credentials')
        DOCKER_IMAGE = 'ruthvikbandi/nodejs-app' // Replace with your Docker Hub image
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Ruthvik406/sample.git' // Replace with your repo
            }
        }

        stage('Build Node.js App') {
            steps {
                sh 'npm install'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${DOCKER_IMAGE}:${env.BUILD_NUMBER}")
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', DOCKER_HUB_CREDENTIALS) {
                        docker.image("${DOCKER_IMAGE}:${env.BUILD_NUMBER}").push()
                    }
                }
            }
        }

        stage('Deploy to EC2') {
            steps {
                script {
                    sh """
                        ssh -o StrictHostKeyChecking=no ec2-user@35.154.81.176 // Replace with your EC2 IP
                            docker pull ${DOCKER_IMAGE}:${env.BUILD_NUMBER}
                            docker stop nodejs-app || true
                            docker rm nodejs-app || true
                            docker run -d --name nodejs-app -p 3000:3000 ${DOCKER_IMAGE}:${env.BUILD_NUMBER}
                        '
                    """
                }
            }
        }
    }
}
