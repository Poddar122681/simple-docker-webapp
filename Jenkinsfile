pipeline {
agent any
environment {
DOCKER_IMAGE = 'poddar122681/simple-docker-webapp'
DOCKER_TAG = 'latest'
}
stages {
stage('Build Docker Image') {
steps {
sh "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} ."
}
}
stage('Push to Docker Hub') {
steps {
withCredentials([usernamePassword(credentialsId: 'docker', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
sh "echo ${DOCKER_PASS} | docker login -u ${DOCKER_USER} --password-stdin"
sh "docker push ${DOCKER_IMAGE}:${DOCKER_TAG}"
sh "docker logout"
}
}
}
}
}
}
