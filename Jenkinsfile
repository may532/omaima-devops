pipeline {
    agent any

    tools {
        maven 'Maven'
        jdk 'JDK17'     // change en JDK11 ou JDK21 si ton pom.xml est différent
    }

    environment {
        DOCKER_IMAGE = 'omaimasahli/spring-k8s-app'
        DOCKER_CREDENTIALS = 'OmaimaSahli'   // ← exactement le nom de ton credential DockerHub
    }

    stages {
        stage('1. Checkout') {
            steps {
                checkout scm
            }
        }

        stage('2. Maven Build & Test') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('3. Build Docker Image') {
            steps {
                sh "docker build -t ${DOCKER_IMAGE}:${BUILD_NUMBER} ."
                sh "docker tag ${DOCKER_IMAGE}:${BUILD_NUMBER} ${DOCKER_IMAGE}:latest"
            }
        }

        stage('4. Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: "${DOCKER_CREDENTIALS}", 
                                                  usernameVariable: 'DOCKER_USER', 
                                                  passwordVariable: 'DOCKER_PASS')]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                    sh "docker push ${DOCKER_IMAGE}:${BUILD_NUMBER}"
                    sh "docker push ${DOCKER_IMAGE}:latest"
                }
            }
        }
    }

    post {
        success {
            echo "✅ Pipeline terminé avec succès !"
        }
        failure {
            echo "❌ Pipeline échoué"
        }
    }
}
