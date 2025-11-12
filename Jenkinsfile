pipeline {
    agent any
    environment {
        DOCKER_HUB_USER = 'subhankar28'
        IMAGE_NAME = 'my-sample-app'
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Subhankar28/my-sample-app.git'
            }
        }
        stage('Install Dependencies') {
    steps {
        sh '''
            python3 -m venv venv
            . venv/bin/activate
            pip install -r requirements.txt
        '''
    }
}
        
stage('Run Tests') {
    steps {
        sh '''
            . venv/bin/activate
            pytest test/test_app.py
        '''
    }
}

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_HUB_USER/$IMAGE_NAME:latest .'
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
                sh 'docker push $DOCKER_HUB_USER/$IMAGE_NAME:latest'
            }
        }
        stage('Deploy to EC2') {
            steps {
                sh 'docker pull $DOCKER_HUB_USER/$IMAGE_NAME:latest'
                sh 'docker stop $(docker ps -q) || true'
                sh 'docker run -d -p 80:5000 $DOCKER_HUB_USER/$IMAGE_NAME:latest'
            }
        }
    }
}
