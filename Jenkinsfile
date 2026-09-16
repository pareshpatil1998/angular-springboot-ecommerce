pipeline {

    agent any

    environment {

        // Java 17 JDK
        JAVA_HOME = '/usr/lib/jvm/java-17-openjdk-amd64'
        PATH = "${JAVA_HOME}/bin:${env.PATH}"
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

                    sh 'npm install --legacy-peer-deps'

                    sh 'npx ng build --configuration production'
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