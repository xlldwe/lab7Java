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
                sh 'chmod +x mvnw'
                sh './mvnw clean compile'
            }
        }

        stage('Test') {
            steps {
                sh './mvnw test'
            }
        }

        stage('Package') {
            steps {
                sh './mvnw package -DskipTests'
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
                    sh "docker build -t ${imageName} ."
                    echo "Docker image ${imageName} built."
                }
            }
        }

        stage('Deploy to Minikube') {
            steps {
                script {
                    sh 'kubectl apply -f k8s/deployment.yaml'
                    sh 'kubectl apply -f k8s/service.yaml'

                    timeout(time: 5, unit: 'MINUTES') {
                        sh 'kubectl rollout status deployment/spring-app-deployment --watch=true'
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