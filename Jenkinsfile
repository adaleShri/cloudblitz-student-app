pipeline {
    agent any

    environment {
        // Docker image names
        BACKEND_IMAGE    = "student-backend"
        FRONTEND_IMAGE   = "student-frontend"

        // Container names
        BACKEND_CONTAINER  = "student-backend"
        FRONTEND_CONTAINER = "student-frontend"

        // DB config for backend container – CHANGE THESE
       DB_URL      = "jdbc:mariadb://student-mariadb:3306/student_db"
       DB_USER     = "root"
       DB_PASSWORD = "studentpass"


        // Host ports
        BACKEND_HOST_PORT = "8081"   // host:8081 -> container:8080
        FRONTEND_HOST_PORT = "80"    // host:80   -> container:80
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build backend image') {
            steps {
                dir('backend') {
                    sh '''
                    echo "Building backend image..."
                    docker build -t ${BACKEND_IMAGE}:latest .
                    '''
                }
            }
        }

        stage('Build frontend image') {
            steps {
                dir('frontend') {
                    sh '''
                    echo "Building frontend image..."
                    docker build -t ${FRONTEND_IMAGE}:latest .
                    '''
                }
            }
        }

        stage('Deploy backend container') {
            steps {
                sh '''
                echo "Stopping old backend container if exists..."
                docker stop ${BACKEND_CONTAINER} || true

                echo "Removing old backend container if exists..."
                docker rm ${BACKEND_CONTAINER} || true

                echo "Starting new backend container on host port ${BACKEND_HOST_PORT}..."
                docker run -d --name ${BACKEND_CONTAINER} \
                  -e SPRING_DATASOURCE_URL=${DB_URL} \
                  -e SPRING_DATASOURCE_USERNAME=${DB_USER} \
                  -e SPRING_DATASOURCE_PASSWORD=${DB_PASSWORD} \
                  -p ${BACKEND_HOST_PORT}:8080 \
                  ${BACKEND_IMAGE}:latest
                '''
            }
        }

        stage('Deploy frontend container') {
            steps {
                sh '''
                echo "Stopping old frontend container if exists..."
                docker stop ${FRONTEND_CONTAINER} || true

                echo "Removing old frontend container if exists..."
                docker rm ${FRONTEND_CONTAINER} || true

                echo "Starting new frontend container on host port ${FRONTEND_HOST_PORT}..."
                docker run -d --name ${FRONTEND_CONTAINER} \
                  -p ${FRONTEND_HOST_PORT}:80 \
                  ${FRONTEND_IMAGE}:latest
                '''
            }
        }
    }

    post {
        success {
            echo "✅ Deployment successful!"
        }
        failure {
            echo "❌ Deployment failed. Check console output."
        }
    }
}
