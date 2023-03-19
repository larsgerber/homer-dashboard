pipeline {
    agent {
        label 'agent-docker'
    }
    environment {
        DATE = new Date().format('yy.M')
        TAG = "${DATE}.${BUILD_NUMBER}"
        IMAGE = 'homer'
        REGISTRY_URL = 'docker.larsgerber.ch'
        REGISTRY_CREDENTIALS = credentials('docker-ci')
    }
    stages {
        stage('Checkout SCM') {
            steps {
                git 'https://github.com/larsgerber/homer-dashboard'
            }
        }
        stage('Docker Build') {
            steps {
                sh "docker build -t ${REGISTRY_URL}/${IMAGE}:${TAG} ."
            }
        }
        stage('Docker Login') {
            steps {
                sh 'echo $REGISTRY_CREDENTIALS_PSW | docker login -u $REGISTRY_CREDENTIALS_USR --password-stdin ${REGISTRY_URL}'
                echo 'Login Completed'
            }
        }
        stage('Docker Push') {
            steps {
                sh "docker push ${REGISTRY_URL}/${IMAGE}:${TAG}"
            }
        }
    }
    post {
        always {
            sh "docker logout ${REGISTRY_URL}"        
        }
    }
}