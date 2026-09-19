pipeline {

    agent any

    environment {
        // Java 17 JDK
        JAVA_HOME = '/usr/lib/jvm/java-17-openjdk-amd64'

        // Java + Node.js
        PATH = "${JAVA_HOME}/bin:/usr/bin:${env.PATH}"
    }

    stages {

        stage('Checkout Code') {
            steps {
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

                    echo "===== JAVA HOME BIN ====="
                    ls -l $JAVA_HOME/bin/java
                    ls -l $JAVA_HOME/bin/javac

                    echo "===== MAVEN ====="
                    cd backend
                    chmod +x mvnw
                    ./mvnw -version
                '''
            }
        }

        stage('Build Backend (Spring Boot)') {
            steps {
                dir('backend') {
                    sh 'chmod +x mvnw'
                    sh './mvnw clean package -DskipTests'
                }
            }
        }

        stage('Build Frontend (Angular)') {
            steps {
                dir('frontend') {
                    sh '''
                        echo "===== NODE ENVIRONMENT ====="
                        echo "PATH=$PATH"

                        echo "===== NODE ====="
                        node -v
                        which node

                        echo "===== NPM ====="
                        npm -v
                        which npm

                        echo "===== ANGULAR BUILD ====="
                        npm install --legacy-peer-deps

                        # Use Angular 16 CLI package to ensure compatibility with project version 16.2.0
                        npx --package @angular/cli@16 ng build --configuration production
                    '''
                }
            }
        }

        stage('Build & Push Docker Images') {
            steps {
                script {
                    dir('backend') {
                        sh 'docker build -t backend-app:latest .'
                    }

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