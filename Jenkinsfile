pipeline {
    agent any

    tools {
        maven 'Maven-3.9'
        jdk 'JDK-17'
    }

    environment {
        DOCKERHUB = 'poddar122681/simple-docker-webappp'
        IMAGE_TAG = 'latest'
    }

    stages {
        // --- YOUR STAGE 1 ---
        stage('Declarative: Checkout SCM') {
            steps {
                echo '=== DECLARATIVE CHECKOUT SCM ==='
                checkout scm
            }
        }

        // --- YOUR STAGE 2 ---
        stage('Checkout SCM') {
            steps {
                echo '=== CHECKOUT SCM ==='
                git branch: 'main', url: 'https://github.com/poddar122681/simple-docker-webappp.git'
            }
        }

        // --- YOUR STAGE 3 ---
        stage('Compile') {
            steps {
                echo '=== COMPILE ==='
                sh 'mvn compile'
            }
        }

        // --- MISSING 1 - FROM FRIEND ---
        stage('Build & Test (Maven)') {
            steps {
                echo '=== BUILD & TEST ==='
                sh 'mvn clean package'
            }
        }

        // --- YOUR STAGE 4 ---
        stage('SonarQube Analysis') {
            steps {
                echo '=== SONARQUBE ANALYSIS ==='
                withSonarQubeEnv('SonarQube') {
                    sh 'mvn sonar:sonar'
                }
            }
        }

        // --- MISSING 2 - FROM FRIEND ---
        stage('Quality Gate') {
            steps {
                echo '=== QUALITY GATE ==='
                timeout(time: 2, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        // --- YOUR STAGE 5 ---
        stage('Build Docker Image') {
            steps {
                echo '=== BUILD DOCKER IMAGE ==='
                sh 'docker build -t $DOCKERHUB:$IMAGE_TAG .'
            }
        }

        // --- YOUR STAGE 6 ---
        stage('Push to Docker Hub') {
            steps {
                echo '=== PUSH TO DOCKER HUB ==='
                withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'U', passwordVariable: 'P')]) {
                    sh 'echo $P | docker login -u $U --password-stdin'
                    sh 'docker push $DOCKERHUB:$IMAGE_TAG'
                }
            }
        }

        // --- MISSING 3 - FROM FRIEND ---
        stage('Deploy') {
            steps {
                echo '=== DEPLOY ==='
                sh 'docker stop webapp || true'
                sh 'docker rm webapp || true'
                sh 'docker run -d --name webapp -p 8081:8080 $DOCKERHUB:$IMAGE_TAG'
                sh 'docker ps | grep webapp'
            }
        }

        // --- MISSING 4 - FROM FRIEND ---
        stage('Health Check') {
            steps {
                echo '=== HEALTH CHECK ==='
                sh 'sleep 5'
                sh 'curl -f http://localhost:8081/'
                echo 'App is UP - Health Check Passed'
            }
        }
    }

    // --- MISSING 5 - FROM FRIEND ---
    post {
        success {
            echo 'PIPELINE SUCCESS - App Deployed Successfully'
        }
        failure {
            echo 'PIPELINE FAILED - Check Console Output'
        }
        always {
            echo 'Cleaning Workspace...'
            cleanWs()
        }
    }
}
