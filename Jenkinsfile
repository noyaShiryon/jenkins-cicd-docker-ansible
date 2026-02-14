pipeline {
    agent any

    // Define parameters for user selection before build
    parameters {
        choice(
            name: 'SERVICE',
            choices: ['service1', 'service2'],
            description: 'Select the service to deploy'
        )
    }

    environment {
        // DockerHub credentials stored in Jenkins credentials store
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-credentials')
        DOCKERHUB_USERNAME    = "noyashiryon"
        // Use selected service as image name
        IMAGE_NAME            = "${params.SERVICE}"
        // Use Jenkins build number as the image version tag
        IMAGE_TAG             = "${BUILD_NUMBER}"
        // Full image name: username/service-name:version
        FULL_IMAGE_NAME       = "${DOCKERHUB_USERNAME}/${IMAGE_NAME}:${IMAGE_TAG}"
    }

    stages {

        // Stage 1: Build Docker image for the selected service
        stage('Build Docker Image') {
            steps {
                echo "Building Docker image for: ${params.SERVICE}"
                sh 'docker build -t ${FULL_IMAGE_NAME} ${SERVICE}'
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

        // Stage 3: Deploy the selected service using Ansible
        stage('Deploy with Ansible') {
            steps {
                echo "Deploying ${params.SERVICE} with Ansible..."
                sh 'ansible-playbook -i inventory.ini deploy-playbook.yml --extra-vars "image_name=${FULL_IMAGE_NAME} service=${params.SERVICE}"'
            }
        }
    }

    post {
        // Always clean up local Docker image after pipeline finishes
        always {
            sh 'docker rmi ${FULL_IMAGE_NAME} || true'
        }
        success {
            echo "${params.SERVICE} deployed successfully!"
        }
        failure {
            echo "${params.SERVICE} deployment failed!"
        }
    }
}1~pipeline {
    agent any

    // Define parameters for user selection before build
    parameters {
        choice(
            name: 'SERVICE',
            choices: ['service1', 'service2'],
            description: 'Select the service to deploy'
        )
    }

    environment {
        // DockerHub credentials stored in Jenkins credentials store
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-credentials')
        DOCKERHUB_USERNAME    = "noyashiryon"
        // Use selected service as image name
        IMAGE_NAME            = "${params.SERVICE}"
        // Use Jenkins build number as the image version tag
        IMAGE_TAG             = "${BUILD_NUMBER}"
        // Full image name: username/service-name:version
        FULL_IMAGE_NAME       = "${DOCKERHUB_USERNAME}/${IMAGE_NAME}:${IMAGE_TAG}"
    }

    stages {

        // Stage 1: Build Docker image for the selected service
        stage('Build Docker Image') {
            steps {
                echo "Building Docker image for: ${params.SERVICE}"
                sh 'docker build -t ${FULL_IMAGE_NAME} ${SERVICE}'
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

        // Stage 3: Deploy the selected service using Ansible
        stage('Deploy with Ansible') {
            steps {
                echo "Deploying ${params.SERVICE} with Ansible..."
                sh 'ansible-playbook -i inventory.ini deploy-playbook.yml --extra-vars "image_name=${FULL_IMAGE_NAME} service=${params.SERVICE}"'
            }
        }
    }

    post {
        // Always clean up local Docker image after pipeline finishes
        always {
            sh 'docker rmi ${FULL_IMAGE_NAME} || true'
        }
        success {
            echo "${params.SERVICE} deployed successfully!"
        }
        failure {
            echo "${params.SERVICE} deployment failed!"
        }
    }
}
