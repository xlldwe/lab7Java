pipeline {
    agent any

    tools {
        jdk 'AdoptOpenJDK-17'
        maven 'Maven-3.9.6'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
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
