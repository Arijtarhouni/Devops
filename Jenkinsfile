pipeline {
    agent any

    environment {
        // Identifiants Docker Hub stockés dans Jenkins
        // (Administrer Jenkins -> Credentials -> Add Credentials -> Username/Password,
        // ID = "dockerhub-credentials")
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-credentials')

        IMAGE_TAG       = "${env.BUILD_NUMBER}"
        BACKEND_IMAGE   = "${DOCKERHUB_CREDENTIALS_USR}/projets-backend"
        FRONTEND_IMAGE  = "${DOCKERHUB_CREDENTIALS_USR}/projets-frontend"
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/Alaa-Rami/DevOps-AppGestionDesProjets.git'
            }
        }

        stage('Build Backend Image') {
            steps {
                dir('backend') {
                    sh "docker build -t ${BACKEND_IMAGE}:${IMAGE_TAG} -t ${BACKEND_IMAGE}:latest ."
                }
            }
        }

        stage('Build Frontend Image') {
            steps {
                dir('frontend') {
                    sh "docker build -t ${FRONTEND_IMAGE}:${IMAGE_TAG} -t ${FRONTEND_IMAGE}:latest ."
                }
            }
        }

        stage('Push Images') {
            steps {
                sh '''
                    echo "$DOCKERHUB_CREDENTIALS_PSW" | docker login -u "$DOCKERHUB_CREDENTIALS_USR" --password-stdin
                '''
                sh "docker push ${BACKEND_IMAGE}:${IMAGE_TAG}"
                sh "docker push ${BACKEND_IMAGE}:latest"
                sh "docker push ${FRONTEND_IMAGE}:${IMAGE_TAG}"
                sh "docker push ${FRONTEND_IMAGE}:latest"
            }
        }

        stage('Deploy Stack') {
            steps {
                sh 'docker compose down || true'
                sh 'docker compose up -d'
            }
        }
    }

    post {
        always {
            sh 'docker logout || true'
        }
        success {
            echo 'Pipeline terminé : images buildées, poussées sur Docker Hub et stack déployée.'
        }
        failure {
            echo 'Le pipeline a échoué — consulter les logs des étapes ci-dessus.'
        }
    }
}
