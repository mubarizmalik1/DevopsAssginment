pipeline {
    agent any

    environment {
        DOCKER_IMAGE_NAME = "mubariz1/cw2-server" 
        GIT_REPO = "https://github.com/mubarizmalik1/DevopsAssginment.git"
        DOCKERHUB_CREDENTIALS = "dockerhub-credentials"
        GITHUB_CREDENTIALS = "github-token"
    }

    stages {
        stage('Clone Repository') {
            steps {
                git credentialsId: 'github-token', url: 'https://github.com/your-username/your-repository'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build(DOCKER_IMAGE_NAME)
                }
            }
        }

        stage('Test Docker Image') {
            steps {
                script {
                    // Run a simple test to verify the image works
                    def dockerContainer = docker.image(DOCKER_IMAGE_NAME).run('-d')
                    sleep(10) // Wait for the container to start
                    dockerContainer.inside('sh -c "echo Hello World"') {
                        echo "Container is running successfully!"
                    }
                }
            }
        }

        stage('Push Docker Image to DockerHub') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: DOCKERHUB_CREDENTIALS, passwordVariable: 'DOCKER_PASSWORD', usernameVariable: 'DOCKER_USERNAME')]) {
                        docker.withRegistry('https://index.docker.io/v1/', DOCKERHUB_CREDENTIALS) {
                            docker.image(DOCKER_IMAGE_NAME).push('latest')
                        }
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    // Deploy to Kubernetes (ensure you have the correct kubeconfig set up)
                    kubernetesDeploy(
                        kubeconfigId: 'kube-config', // Your Kubernetes credentials
                        configs: 'k8s-deployment.yaml' // Path to your Kubernetes config file
                    )
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline executed successfully!'
        }
        failure {
            echo 'Pipeline execution failed.'
        }
    }
}

