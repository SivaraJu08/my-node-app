pipeline {
    agent any

    environment {
        // Define the Docker Hub credentials and repository details
        DOCKER_HUB_CREDENTIALS = 'Raju@2001' // Jenkins credentials ID for Docker Hub
        DOCKER_HUB_REPO = 'raju8/my-node-app' // Replace with your Docker Hub repository name
        GIT_REPO = 'https://github.com/SivaraJu08/my-node-app.git' // Replace with your GitHub repository URL
    }

    stages {
        stage('Checkout Code') {
            steps {
                script {
                    // Clone the repository from GitHub
                    git url: "${GIT_REPO}", branch: 'main'  // Adjust the branch name if necessary
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Get the current commit hash and branch name
                    def commitHash = sh(script: 'git rev-parse --short HEAD', returnStdout: true).trim()
                    def branchName = sh(script: 'git rev-parse --abbrev-ref HEAD', returnStdout: true).trim()

                    // Build Docker image and tag it with commit hash and branch name
                    def imageTag = "${branchName}-${commitHash}"
                    sh "docker build -t ${DOCKER_HUB_REPO}:${imageTag} ."
                }
            }
        }

        stage('Push Docker Image to Docker Hub') {
            steps {
                script {
                    // Login to Docker Hub using Jenkins credentials
                    withDockerRegistry([credentialsId: "${DOCKER_HUB_CREDENTIALS}", url: "https://index.docker.io/v1/"]) {
                        // Push the image to Docker Hub
                        def commitHash = sh(script: 'git rev-parse --short HEAD', returnStdout: true).trim()
                        def branchName = sh(script: 'git rev-parse --abbrev-ref HEAD', returnStdout: true).trim()
                        def imageTag = "${branchName}-${commitHash}"

                        sh "docker push ${DOCKER_HUB_REPO}:${imageTag}"
                    }
                }
            }
        }
    }

    post {
        always {
            // Clean up any Docker images after the job finishes
            sh "docker system prune -f"
        }
    }
}
