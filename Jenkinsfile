pipeline {
    agent any
    
    stages {
        stage('Checkout Code') {
            steps {
                // Pulls code from your GitHub repository
                checkout scm
            }
        }
        
        stage('Build Backend (Spring Boot)') {
            steps {
                dir('backend') {
                    // Uses Maven wrapper inside the backend folder to build the jar
                    sh 'chmod +x mvnw'
                    sh './mvnw clean package -DskipTests'
                }
            }
        }
        
        stage('Build Frontend (Angular)') {
            steps {
                dir('frontend') {
                    // Installs node modules and builds Angular production assets
                    sh 'npm install --legacy-peer-deps'
                    sh 'npx ng build --configuration production'
                }
            }
        }
        
        stage('Build & Push Docker Images') {
            steps {
                script {
                    // Build Backend Docker Image
                    dir('backend') {
                        // Assumes a Dockerfile exists in your backend folder
                        sh 'docker build -t backend-app:latest .'
                    }
                    
                    // Build Frontend Docker Image
                    dir('frontend') {
                        // Uses the Dockerfile shown in your VS Code screenshot
                        sh 'docker build -t frontend-app:latest .'
                    }
                }
            }
        }
    }
    
    post {
        always {
            echo 'Pipeline execution completed!'
        }
        failure {
            echo 'Pipeline failed. Check the logs for details.'
        }
    }
}