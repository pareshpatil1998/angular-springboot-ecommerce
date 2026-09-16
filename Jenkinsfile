pipeline {

    agent any

    environment {
        // Java 21 configuration
        JAVA_HOME = '/usr/lib/jvm/java-21-openjdk-amd64'
        PATH = "${JAVA_HOME}/bin:${env.PATH}"
    }

    stages {

        stage('Checkout Code') {

            steps {

                // Pulls code from your GitHub repository
                checkout scm
            }
        }

        stage('Check Java') {

            steps {

                sh '''
                    echo "===== JAVA ENVIRONMENT ====="
                    echo "JAVA_HOME=$JAVA_HOME"
                    echo "PATH=$PATH"

                    echo "===== JAVA ====="
                    java -version
                    which java

                    echo "===== JAVAC ====="
                    javac -version
                    which javac

                    echo "===== JAVA_HOME BIN ====="
                    ls -l $JAVA_HOME/bin/java
                    ls -l $JAVA_HOME/bin/javac

                    echo "===== MAVEN ====="
                    ./backend/mvnw -version
                '''
            }
        }

        stage('Build Backend (Spring Boot)') {

            steps {

                dir('backend') {

                    // Give Maven wrapper execute permission
                    sh 'chmod +x mvnw'

                    // Build Spring Boot application
                    sh './mvnw clean package -DskipTests'
                }
            }
        }

        stage('Build Frontend (Angular)') {

            steps {

                dir('frontend') {

                    // Install Node dependencies
                    sh 'npm install --legacy-peer-deps'

                    // Build Angular production assets
                    sh 'npx ng build --configuration production'
                }
            }
        }

        stage('Build & Push Docker Images') {

            steps {

                script {

                    // Build Backend Docker Image
                    dir('backend') {

                        sh 'docker build -t backend-app:latest .'
                    }

                    // Build Frontend Docker Image
                    dir('frontend') {

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