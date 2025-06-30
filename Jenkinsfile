pipeline {
    agent any

    tools {
        maven 'Maven 3' // Nom de l'installation Maven dans Jenkins (à configurer)
    }

    environment {
        DOCKER_IMAGE = 'boulki/springboot-app:arman-app' // Ton image Docker complète
        DOCKER_REGISTRY = '' // Si tu pushes vers un registry, mettre ici
        // Par exemple 'docker.io/boulki/springboot-app:arman-app'
    }

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
                    // Build depuis le Dockerfile à la racine (../Dockerfile) avec contexte racine (..)
                    sh "docker build -f ../Dockerfile -t ${DOCKER_IMAGE} .."
                }
            }
        }

        stage('Push Docker Image') {
            when {
                expression { env.DOCKER_REGISTRY != '' }
            }
            steps {
                sh "docker push ${DOCKER_IMAGE}"
            }
        }
    }

    post {
        success {
            echo "Build et push terminés avec succès."
        }
        failure {
            echo "Erreur durant la pipeline."
        }
    }
}

