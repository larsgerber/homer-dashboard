pipeline {
    agent {
        label 'agent-docker'
    }
    environment {
        REGISTRY_URL = 'docker.larsgerber.ch'
        REGISTRY_CREDENTIALS = credentials('docker-ci')
        REPOSITORY_NAME = 'larsgerber/homer-dashboard'
        DATE = new Date().format('yy.M')
        TAG = "${DATE}.${BUILD_NUMBER}"
        IMAGE = 'homer'
        IMAGE_FULL = "${REGISTRY_URL}/${IMAGE}:${TAG}"
        CONTAINER_NAME = "test-build-${IMAGE}-${BUILD_NUMBER}"
        DISCORD_WEBHOOK = credentials('discord_webhook_private')
    }
    stages {
        stage('Checkout SCM') {
            steps {
                git credentialsId: 'github', url: "git@github.com:${REPOSITORY_NAME}.git"
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
            discordSend title: JOB_NAME,
                        link: BUILD_URL,
                        description: "**Build:** ${BUILD_NUMBER}\n**Status:** ${currentBuild.result}\n\u2060",
                        showChangeset: true,
                        scmWebUrl: "https://github.com/${REPOSITORY_NAME}/commit/%s",
                        footer: "Build finished in ${currentBuild.durationString.replace(' and counting', '')}.",
                        result: currentBuild.currentResult,
                        webhookURL: "${DISCORD_WEBHOOK}"
        }
    }
}