pipeline {
    agent any  // Run on any available Jenkins agent

    environment {
        // DockerHub credentials stored in Jenkins credentials store
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-credentials')
        DOCKERHUB_USERNAME    = "noyashiryon"
        IMAGE_NAME            = "my-app"
        // Use Jenkins build number as the image version tag
        IMAGE_TAG             = "${BUILD_NUMBER}"
        // Full image name: username/image-name:version
        FULL_IMAGE_NAME       = "${DOCKERHUB_USERNAME}/${IMAGE_NAME}:${IMAGE_TAG}"
    }

    stages {

        // Stage 1: Build the Docker image from the Dockerfile
        stage('Build Docker Image') {
            steps {
                echo "Building Docker image: ${FULL_IMAGE_NAME}"
                sh 'docker build -t ${FULL_IMAGE_NAME} .'
            }
        }

        // Stage 2: Push the image to DockerHub
        stage('Push to DockerHub') {
            steps {
                echo "Pushing image to DockerHub..."
                sh '''
                    # Login to DockerHub using Jenkins credentials
                    echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin
                    # Push the tagged image
                    docker push ${FULL_IMAGE_NAME}
                    # Logout for security
                    docker logout
                '''
            }
        }

        // Stage 3: Deploy the application using Ansible
        stage('Deploy with Ansible') {
            steps {
                echo "Deploying with Ansible..."
                sh 'ansible-playbook -i inventory.ini deploy-playbook.yml'
            }
        }
    }

    post {
        // Always clean up local Docker image after pipeline finishes
        always {
            sh 'docker rmi ${FULL_IMAGE_NAME} || true'
        }
        success {
            echo "Pipeline completed successfully!"
        }
        failure {
            echo "Pipeline failed!"
        }
    }
}
