pipeline {
    agent any

    environment {
        GIT_REPO = 'https://github.com/SivaraJu08/my-node-app.git' // Replace with your GitHub repository URL
        CREDENTIALS_ID = 'git-credentials' // Replace with your Jenkins credentials ID for GitHub
        DOCKER_HUB_REPO = 'raju8/my-node-app' // Replace with your Docker Hub repository name
        DOCKER_HUB_CREDENTIALS = 'docker-hub-credentials' // Jenkins credentials ID for Docker Hub
    }

    stages {
        stage('Checkout Code') {
            steps {
                script {
                    // Checkout the code from GitHub using the credentials
                    checkout([
                        $class: 'GitSCM',
                        branches: [[name: '*/main']],
                        userRemoteConfigs: [[url: "${GIT_REPO}", credentialsId: "${CREDENTIALS_ID}"]]
                    ])
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Get commit hash and branch name
                    def commitHash = bat(script: 'git rev-parse --short HEAD', returnStdout: true).trim()
                    def branchName = bat(script: 'git rev-parse --abbrev-ref HEAD', returnStdout: true).trim()

                    // Build the Docker image
                    def imageTag = "${branchName}-${commitHash}"
                    bat "docker build -t ${DOCKER_HUB_REPO}:${imageTag} ."
                }
            }
        }

        stage('Push Docker Image to Docker Hub') {
            steps {
                script {
                    // Login to Docker Hub and push the image
                    withDockerRegistry([credentialsId: "${DOCKER_HUB_CREDENTIALS}", url: "https://index.docker.io/v1/"]) {
                        def commitHash = bat(script: 'git rev-parse --short HEAD', returnStdout: true).trim()
                        def branchName = bat(script: 'git rev-parse --abbrev-ref HEAD', returnStdout: true).trim()
                        def imageTag = "${branchName}-${commitHash}"

                        bat "docker push ${DOCKER_HUB_REPO}:${imageTag}"
                    }
                }
            }
        }
    }

    post {
        always {
            script {
                // Cleanup Docker system
                bat "docker system prune -f"
            }
        }
    }
}
