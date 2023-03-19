pipeline {
    agent {
        label 'agent-docker'
    }
    environment {
        DATE = new Date().format('yy.M')
        TAG = "${DATE}.${BUILD_NUMBER}"
        IMAGE = 'homer'
        CONTAINER_NAME = "test-build-${IMAGE}-${BUILD_NUMBER}"
        REGISTRY_URL = 'docker.larsgerber.ch'
        REGISTRY_CREDENTIALS = credentials('docker-ci')
        IMAGE_FULL = "${REGISTRY_URL}/${IMAGE}:${TAG}"
    }
    stages {
        stage('Checkout SCM') {
            steps {
                git credentialsId: 'github', url: 'git@github.com:larsgerber/homer-dashboard.git'
            }
        }
        stage('Docker Build') {
            steps {
                sh "docker build -t ${IMAGE_FULL} ."
            }
        }
        stage('Docker Test') {
            steps {
                sh "docker run --rm -d --network jenkins-agent --name ${CONTAINER_NAME} ${IMAGE_FULL}"
                sh 'curl -s ${CONTAINER_NAME}:8080 | grep "app-mount"'
            }
        }
        stage('Docker Login') {
            steps {
                sh 'echo $REGISTRY_CREDENTIALS_PSW | docker login -u $REGISTRY_CREDENTIALS_USR --password-stdin ${REGISTRY_URL}'
            }
        }
        stage('Docker Push') {
            steps {
                sh "docker push ${IMAGE_FULL}"
            }
        }
    }
    post {
        always {
            sh "docker logout ${REGISTRY_URL}"
            sh "docker stop ${CONTAINER_NAME} || true"
        }
    }
}