pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'akarman/springboot-arman'  // Ton repo DockerHub exact
        SONARQUBE = 'SonarQube'                    // Nom du serveur Sonar configuré dans Jenkins
    }

    tools {
        maven 'Maven 3' // Nom défini dans Jenkins > Global Tool Configuration
    }

    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/Ak-Arman/springboot-arman.git', credentialsId: 'dockerhub-creds'
            }
        }

        stage('Test Maven') {
            steps {
                dir('demo-github') {
                    sh 'mvn clean test'
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
                dir('demo-github') {
                    withSonarQubeEnv("${SONARQUBE}") {
                        sh 'mvn sonar:sonar'
                    }
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                dir('demo-github') {
                    sh 'docker build -t $DOCKER_IMAGE .'
                }
            }
        }

        stage('Trivy Scan (optional)') {
            steps {
                dir('demo-github') {
                    sh 'trivy image $DOCKER_IMAGE || true'
                }
            }
        }

        stage('Push to DockerHub') {
            steps {
                dir('demo-github') {
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
}

