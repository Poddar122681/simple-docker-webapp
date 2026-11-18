pipeline {
    agent any

    tools {
        maven 'Maven3'
        jdk 'JDK21'
    }

    environment {
        DOCKERHUB_CRED_ID = 'docker'
        DOCKERHUB_REPO = 'YOUR_DOCKERHUB_USERNAME/simple-docker-webapp'
        IMAGE_TAG = "${BUILD_NUMBER}"
        CONTAINER_NAME = 'simple-docker-webapp'
        APP_PORT = '8081'

        // Replace with your Docker EC2 private IP
        DOCKER_HOST = 'YOUR_DOCKER_EC2_PRIVATE_IP'
        DOCKER_SSH_USER = 'ubuntu'
    }

    options {
        timestamps()
        disableConcurrentBuilds()
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/poddar122681/simple-docker-webapp.git'
            }
        }

        stage('Build and Test') {
            steps {
                sh 'mvn -B clean verify'
            }
            post {
                always {
                    junit testResults: 'target/surefire-reports/*.xml',
                          allowEmptyResults: true
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonarqube') {
                    sh '''
                        mvn -B sonar:sonar \
                        -Dsonar.projectKey=simple-docker-webapp \
                        -Dsonar.projectName=simple-docker-webapp
                    '''
                }
            }
        }

        stage('Quality Gate') {
            steps {
                timeout(time: 5, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                    docker build \
                    -t ${DOCKERHUB_REPO}:${IMAGE_TAG} \
                    -t ${DOCKERHUB_REPO}:latest .
                '''
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: "${DOCKERHUB_CRED_ID}",
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )
                ]) {
                    sh '''
                        set +x
                        echo "$DOCKER_PASS" | docker login \
                        -u "$DOCKER_USER" --password-stdin

                        docker push ${DOCKERHUB_REPO}:${IMAGE_TAG}
                        docker push ${DOCKERHUB_REPO}:latest

                        docker logout
                    '''
                }
            }
        }

        stage('Deploy to Docker EC2') {
            steps {
                sshagent(credentials: ['docker-ec2-ssh']) {
                    sh '''
                        ssh -o StrictHostKeyChecking=no \
                        ${DOCKER_SSH_USER}@${DOCKER_HOST} \
                        "set -e;
                        docker pull ${DOCKERHUB_REPO}:${IMAGE_TAG};
                        docker rm -f ${CONTAINER_NAME} || true;
                        docker run -d \
                        --name ${CONTAINER_NAME} \
                        -p ${APP_PORT}:8080 \
                        --restart unless-stopped \
                        ${DOCKERHUB_REPO}:${IMAGE_TAG}"
                    '''
                }
            }
        }

        stage('Health Check') {
            steps {
                sshagent(credentials: ['docker-ec2-ssh']) {
                    sh '''
                        ssh -o StrictHostKeyChecking=no \
                        ${DOCKER_SSH_USER}@${DOCKER_HOST} \
                        "for i in \\$(seq 1 10); do
                            if curl -fsS http://localhost:${APP_PORT}/actuator/health; then
                                echo 'App is healthy'
                                exit 0
                            fi
                            echo 'Waiting for application...'
                            sleep 5
                        done
                        echo 'Health check failed'
                        docker logs ${CONTAINER_NAME} --tail 50
                        exit 1"
                    '''
                }
            }
        }
    }
}[3:55 PM]pipeline {
    agent any

    tools {
        maven 'Maven3'
        jdk 'JDK21'
    }

    environment {
        DOCKERHUB_CRED_ID = 'docker'
        DOCKERHUB_REPO = 'YOUR_DOCKERHUB_USERNAME/simple-docker-webapp'
        IMAGE_TAG = "${BUILD_NUMBER}"
        CONTAINER_NAME = 'simple-docker-webapp'
        APP_PORT = '8081'
        DOCKER_HOST = 'YOUR_DOCKER_EC2_PRIVATE_IP'
        DOCKER_SSH_USER = 'ubuntu'
    }

    options {
        timestamps()
        disableConcurrentBuilds()
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/poddar122681/simple-docker-webapp.git'
            }
        }

        stage('Build and Test') {
            steps {
                sh 'mvn -B clean verify'
            }
            post {
                always {
                    junit testResults: 'target/surefire-reports/*.xml',
                          allowEmptyResults: true
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonarqube') {
                    sh '''
                        mvn -B sonar:sonar \
                        -Dsonar.projectKey=simple-docker-webapp \
                        -Dsonar.projectName=simple-docker-webapp
                    '''
                }
            }
        }

        stage('Quality Gate') {
            steps {
                timeout(time: 5, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                    docker build \
                    -t ${DOCKERHUB_REPO}:${IMAGE_TAG} \
                    -t ${DOCKERHUB_REPO}:latest .
                '''
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'docker',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )
                ]) {
                    sh '''
                        set +x
                        echo "$DOCKER_PASS" | docker login \
                        -u "$DOCKER_USER" --password-stdin

                        docker push ${DOCKERHUB_REPO}:${IMAGE_TAG}
                        docker push ${DOCKERHUB_REPO}:latest

                        docker logout
                    '''
                }
            }
        }

        stage('Deploy to Docker EC2') {
            steps {
                sshagent(credentials: ['docker-ec2-ssh']) {
                    sh '''
                        ssh -o StrictHostKeyChecking=no \
                        ${DOCKER_SSH_USER}@${DOCKER_HOST} \
                        "set -e;
                        docker pull ${DOCKERHUB_REPO}:${IMAGE_TAG};
                        docker rm -f ${CONTAINER_NAME} || true;
                        docker run -d \
                        --name ${CONTAINER_NAME} \
                        -p ${APP_PORT}:8080 \
                        --restart unless-stopped \
                        ${DOCKERHUB_REPO}:${IMAGE_TAG}"
                    '''
                }
            }
        }

        stage('Health Check') {
            steps {
                sshagent(credentials: ['docker-ec2-ssh']) {
                    sh '''
                        ssh -o StrictHostKeyChecking=no \
                        ${DOCKER_SSH_USER}@${DOCKER_HOST} \
                        "for i in \\$(seq 1 10); do
                            if curl -fsS http://localhost:${APP_PORT}/actuator/health; then
                                echo 'App is healthy'
                                exit 0
                            fi
                            echo 'Waiting for application...'
                            sleep 5
                        done
                        echo 'Health check failed'
                        docker logs ${CONTAINER_NAME} --tail 50
                        exit 1"
                    '''
                }
            }
        }
    }
}
