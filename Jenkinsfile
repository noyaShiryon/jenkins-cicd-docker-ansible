pipeline {
    agent any

    parameters {
        choice(
            name: 'SERVICE',
            choices: ['service1', 'service2'],
            description: 'Select the service to deploy'
        )
    }

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-credentials')
        DOCKERHUB_USERNAME    = "noyashiryon"
        IMAGE_NAME            = "${params.SERVICE}"
        IMAGE_TAG             = "${BUILD_NUMBER}"
        FULL_IMAGE_NAME       = "${DOCKERHUB_USERNAME}/${IMAGE_NAME}:${IMAGE_TAG}"
    }

    stages {

        stage('Build Docker Image') {
            steps {
                echo "Building Docker image for: ${params.SERVICE}"
                sh "docker build -t ${FULL_IMAGE_NAME} ${params.SERVICE}"
            }
        }

        stage('Push to DockerHub') {
            steps {
                echo "Pushing image to DockerHub..."
                sh """
                    echo \$DOCKERHUB_CREDENTIALS_PSW | docker login -u \$DOCKERHUB_CREDENTIALS_USR --password-stdin
                    docker push ${FULL_IMAGE_NAME}
                    docker logout
                """
            }
        }

        stage('Deploy with Ansible') {
            steps {
                echo "Deploying ${params.SERVICE} with Ansible..."
                sh "ansible-playbook -i inventory.ini deploy-playbook.yml --extra-vars \"image_name=${FULL_IMAGE_NAME} service=${params.SERVICE}\""
            }
        }
    }

    post {
        always {
            sh "docker rmi ${FULL_IMAGE_NAME} || true"
        }
        success {
            echo "${params.SERVICE} deployed successfully!"
        }
        failure {
            echo "${params.SERVICE} deployment failed!"
        }
    }
}
