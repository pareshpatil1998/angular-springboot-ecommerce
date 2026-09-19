pipeline {

    agent any

    options {
        // Prevent Jenkins Declarative Pipeline from doing
        // an automatic checkout before our Checkout Code stage.
        skipDefaultCheckout(true)
    }

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
                    echo "========================================"
                    echo "        JAVA ENVIRONMENT"
                    echo "========================================"

                    echo "JAVA_HOME=$JAVA_HOME"
                    echo "PATH=$PATH"

                    echo ""
                    echo "===== JAVA ====="
                    java -version
                    which java

                    echo ""
                    echo "===== JAVAC ====="
                    javac -version
                    which javac

                    echo ""
                    echo "===== JAVA HOME BIN ====="
                    ls -l $JAVA_HOME/bin/java
                    ls -l $JAVA_HOME/bin/javac

                    echo ""
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
                    sh '''
                        echo "========================================"
                        echo "       BUILDING SPRING BOOT"
                        echo "========================================"

                        chmod +x mvnw

                        ./mvnw clean package -DskipTests
                    '''
                }
            }
        }

        stage('Build Frontend (Angular)') {
            steps {
                dir('frontend') {
                    sh '''
                        echo "========================================"
                        echo "        NODE ENVIRONMENT"
                        echo "========================================"

                        node -v
                        npm -v

                        echo ""
                        echo "========================================"
                        echo "        INSTALL DEPENDENCIES"
                        echo "========================================"

                        # Do NOT delete node_modules or package-lock.json.
                        # npm ci installs dependencies from package-lock.json.

                        npm ci --legacy-peer-deps

                        echo ""
                        echo "========================================"
                        echo "        ANGULAR VERSION"
                        echo "========================================"

                        ./node_modules/.bin/ng version

                        echo ""
                        echo "========================================"
                        echo "        ANGULAR PRODUCTION BUILD"
                        echo "========================================"

                        ./node_modules/.bin/ng build --configuration production
                    '''
                }
            }
        }

        stage('Build Docker Images') {
            steps {
                script {

                    echo "========================================"
                    echo "        BUILDING DOCKER IMAGES"
                    echo "========================================"

                    dir('backend') {
                        sh '''
                            docker build -t backend-app:latest .
                        '''
                    }

                    dir('frontend') {
                        sh '''
                            docker build -t frontend-app:latest .
                        '''
                    }
                }
            }
        }
    }

    post {

        always {
            echo 'Pipeline execution completed!'
        }

        success {
            echo 'Pipeline completed successfully!'
        }

        failure {
            echo 'Pipeline failed. Check the logs for details.'
        }
    }
}