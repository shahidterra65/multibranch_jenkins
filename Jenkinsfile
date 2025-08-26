pipeline {
    agent any

    // No 'pollSCM' here — pipeline only runs when you click 'Build Now'
    // or when triggered by a webhook from GitHub/GitLab/Bitbucket, etc.

    stages {
        stage('Checkout Code') {
            steps {
                echo ' Cloning latest code from repository...'
                // If using "Pipeline script from SCM" in Jenkins job config,
                // 'checkout scm' will pull latest automatically.
                checkout scm
            }
        }

        stage('Stop & Remove Old Container') {
            steps {
                script {
                    def containerName = "myapp_container"
                    echo " Stopping and removing old container if it exists..."
                    sh """
                        docker ps -q --filter name=${containerName} | grep -q . && docker stop ${containerName} && docker rm ${containerName} || true
                    """
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    def imageName = "myapp:latest"
                    echo " Building Docker image: ${imageName}"
                    sh "docker build -t ${imageName} ."
                }
            }
        }

        stage('Run New Container') {
            steps {
                script {
                    def containerName = "myapp_container"
                    echo " Running new container on port 3000:80..."
                    sh "docker run -d --name ${containerName} -p 3000:80 myapp:latest"
                }
            }
        }
    }

    post {
        success {
            echo ' Deployment successful! App is live on port 80.'
        }
        failure {
            echo ' Deployment failed — check Jenkins console output.'
        }
    }
}
