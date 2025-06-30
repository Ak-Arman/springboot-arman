pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                dir('demo-github') {
                    sh 'mvn clean test'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                dir('demo-github') {
                    sh 'docker build -t boulki/springboot-app:arman-app .'
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKERHUB_USER', passwordVariable: 'DOCKERHUB_PASS')]) {
                    sh 'docker login -u $DOCKERHUB_USER -p $DOCKERHUB_PASS'
                    sh 'docker push boulki/springboot-app:arman-app'
                }
            }
        }
    }
}

