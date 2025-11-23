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
        DB_URL      = "jdbc:mariadb://<db-host>:3306/student_db"
        DB_USER     = "<db-user>"
        DB_PASSWORD = "<db-pass>"
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
                if [ $(docker ps -q -f name=${BACKEND_CONTAINER}) ]; then
                    docker stop ${BACKEND_CONTAINER}
                fi

                echo "Removing old backend container if exists..."
                if [ $(docker ps -aq -f name=${BACKEND_CONTAINER}) ]; then
                    docker rm ${BACKEND_CONTAINER}
                fi

                echo "Starting new backend container..."
                docker run -d --name ${BACKEND_CONTAINER} \
                  -e SPRING_DATASOURCE_URL=${DB_URL} \
                  -e SPRING_DATASOURCE_USERNAME=${DB_USER} \
                  -e SPRING_DATASOURCE_PASSWORD=${DB_PASSWORD} \
                  -p 8080:8080 \
                  ${BACKEND_IMAGE}:latest
                '''
            }
        }

        stage('Deploy frontend container') {
            steps {
                sh '''
                echo "Stopping old frontend container if exists..."
                if [ $(docker ps -q -f name=${FRONTEND_CONTAINER}) ]; then
                    docker stop ${FRONTEND_CONTAINER}
                fi

                echo "Removing old frontend container if exists..."
                if [ $(docker ps -aq -f name=${FRONTEND_CONTAINER}) ]; then
                    docker rm ${FRONTEND_CONTAINER}
                fi

                echo "Starting new frontend container..."
                docker run -d --name ${FRONTEND_CONTAINER} \
                  -p 80:80 \
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
