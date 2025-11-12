pipeline {
    agent any
    environment {
        DOCKER_HUB_USER = 'your-dockerhub-username'
        DOCKER_IMAGE = "${DOCKER_HUB_USER}/my-sample-app"
    }
    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/<your-username>/my-sample-app.git'
            }
        }
        stage('Install Dependencies') {
            steps {
                sh 'pip install -r requirements.txt'
            }
        }
        stage('Run Tests') {
            steps {
                sh 'pytest tests/'
            }
        }
        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE:latest .'
            }
        }
        stage('Login to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh 'echo $PASS | docker login -u $USER --password-stdin'
                }
            }
        }
        stage('Push to Docker Hub') {
            steps {
                sh 'docker push $DOCKER_IMAGE:latest'
            }
        }
        stage('Deploy to EC2') {
            steps {
                sh 'docker pull $DOCKER_IMAGE:latest'
                sh 'docker run -d -p 80:5000 $DOCKER_IMAGE:latest'
            }
        }
    }
}
