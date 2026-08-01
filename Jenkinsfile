pipeline {
    agent any

    environment {
        DOCKER_HUB_USER  = 'zehavab'
        IMAGE_NAME       = 'flask-vision-board'
        IMAGE_TAG        = "${BUILD_NUMBER}"
        DOCKER_HUB_CREDS = 'dockerhub-creds'
    }

    stages {
        stage('Build Docker Image') {
            steps {
                echo 'Building the Docker image...'
                sh "docker build -t ${DOCKER_HUB_USER}/${IMAGE_NAME}:${IMAGE_TAG} ."
                sh "docker tag ${DOCKER_HUB_USER}/${IMAGE_NAME}:${IMAGE_TAG} ${DOCKER_HUB_USER}/${IMAGE_NAME}:latest"
            }
        }

        stage('Push to DockerHub') {
            steps {
                echo 'Logging into DockerHub and pushing image...'
                withCredentials([usernamePassword(credentialsId: "${DOCKER_HUB_CREDS}", usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh "echo ${PASS} | docker login -u ${USER} --password-stdin"
                    sh "docker push ${DOCKER_HUB_USER}/${IMAGE_NAME}:${IMAGE_TAG}"
                    sh "docker push ${DOCKER_HUB_USER}/${IMAGE_NAME}:latest"
                }
            }
        }

        stage('Deployment with Ansible') {
            steps {
                echo 'Running Ansible playbook to deploy the application...'
                sh "ansible-playbook -i inventory.ini deploy-playbook.yml"
            }
        }
    }

    post {
        always {
            echo 'Cleaning up local Docker images to save space...'
            sh "docker rmi ${DOCKER_HUB_USER}/${IMAGE_NAME}:${IMAGE_TAG} || true"
            sh "docker rmi ${DOCKER_HUB_USER}/${IMAGE_NAME}:latest || true"
            sh "docker logout"
        }
        success {
            echo 'Pipeline completed successfully! 🎉'
        }
        failure {
            echo 'Pipeline failed. Please check the logs. ❌'
        }
    }
}
