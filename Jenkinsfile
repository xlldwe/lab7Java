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
                bat './mvnw clean compile'
            }
        }

        stage('Test') {
            steps {
                bat './mvnw test'
            }
        }

        stage('Package') {
            steps {
                bat './mvnw package -DskipTests'
            }
        }

        stage('Archive Artifacts') {
            steps {
                archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    def imageName = "spring-app:latest"
                    bat "docker build -t ${imageName} ."
                    echo "Docker image ${imageName} built."
                }
            }
        }

        stage('Deploy to Minikube') {
            steps {
                script {
                    bat 'kubectl apply -f k8s\\deployment.yaml'
                    bat 'kubectl apply -f k8s\\service.yaml'

                    timeout(time: 5, unit: 'MINUTES') {
                        bat 'kubectl rollout status deployment/spring-app-deployment --watch=true'
                    }

                    echo "Application deployed to Minikube."
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline завершено успішно.'
        }
        failure {
            echo 'Pipeline завершився з помилкою.'
        }
    }
}