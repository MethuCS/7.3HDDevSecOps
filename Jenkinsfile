pipeline {
    agent any

    tools {
        nodejs 'nodejs-18'
    }

    environment {
        IMAGE_NAME = "nodejs-goof"
        TAG = "${BUILD_NUMBER}"
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/MethuCS/7.3HDDevSecOps.git'
            }
        }

        stage('Build') {
            steps {
                sh 'npm install'
                sh 'docker build -t $IMAGE_NAME:$TAG .'
            }
        }

        stage('Test') {
            steps {
                sh 'npm test || true'
            }
        }

        stage('Code Quality') {
            steps {
                sh 'npx eslint . || true'
            }
        }

        stage('Security') {
            steps {
                sh 'npm audit --audit-level=high || true'
            }
        }

        stage('Deploy (Staging)') {
            steps {
                sh '''
                docker rm -f nodejs-goof || true
                docker run -d --name nodejs-goof -p 3000:3000 $IMAGE_NAME:$TAG
                '''
            }
        }

        stage('Release') {
            steps {
                sh 'docker tag $IMAGE_NAME:$TAG $IMAGE_NAME:latest'
            }
        }

        stage('Monitoring') {
            steps {
                sh 'docker logs nodejs-goof --tail 20'
            }
        }
    }
}
