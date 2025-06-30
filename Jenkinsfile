pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'akarman/springboot-arman'  // Remplace par ton repo DockerHub exact
        SONARQUBE = 'SonarQube' // Nom du serveur Sonar configuré dans Jenkins
    }

    tools {
        maven 'Maven 3'   // Nom que tu as défini dans Jenkins > Global Tools
    }

    stages {
        stage('Checkout') {
            steps {
                git 'git@github.com:Ak-Arman/springboot-arman.git'
            }
        }

        stage('Test Maven') {
            steps {
                sh 'mvn clean test'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv("${SONARQUBE}") {
                    sh 'mvn sonar:sonar'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE .'
            }
        }

        stage('Trivy Scan (optional)') {
            steps {
                sh 'trivy image $DOCKER_IMAGE || true'
            }
        }

        stage('Push to DockerHub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh '''
                      echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                      docker push $DOCKER_IMAGE
                    '''
                }
            }
        }
    }
}

