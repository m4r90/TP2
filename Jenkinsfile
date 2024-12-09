pipeline {
    agent any
    environment {
        DOCKER_IMAGE = "m4r90/TP2"
    }
    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/m4r90/TP2.git'
            }
        }
        stage('Run Tests') {
            steps {
                sh 'pip install -r requirements.txt'
                sh 'python -m unittest discover tests'
            }
        }
        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE .'
            }
        }
        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', usernameVariable: 'm4r90', passwordVariable: 'DOCKER_PASSWORD')]) {
                    sh 'echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin'
                    sh 'docker push $DOCKER_IMAGE'
                }
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                sh 'kubectl apply -f deployment.yaml'
                sh 'kubectl apply -f service.yaml'
            }
        }
        stage('Security Scan') {
            steps {
                sh 'trivy image $DOCKER_IMAGE || exit 1'
            }
        }
    }
}