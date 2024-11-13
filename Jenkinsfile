pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'my-app:latest'
        DOCKER_REGISTRY = 'dockerhub'
    }

    stages {
        stage('Checkout') {
            steps {
                // Checkout the source code from GitHub using credentials
                git credentialsId: 'github-cred', url: 'https://github.com/user/repo.git'
            }
        }

        stage('Build') {
            steps {
                // Run Maven to build the project
                sh 'mvn clean install'
            }
        }

        stage('Test') {
            steps {
                // Run tests (e.g., unit tests) using Maven
                sh 'mvn test'
            }
        }

        stage('Docker Build') {
            steps {
                script {
                    // Build a Docker image
                    sh "docker build -t $DOCKER_REGISTRY/$DOCKER_IMAGE ."
                }
            }
        }

        stage('Docker Push') {
            steps {
                script {
                    // Log in to DockerHub using credentials
                    withCredentials([usernamePassword(credentialsId: 'dockerhub-cred', passwordVariable: 'DOCKER_PASSWORD', usernameVariable: 'DOCKER_USERNAME')]) {
                        sh "docker login -u $DOCKER_USERNAME -p $DOCKER_PASSWORD"
                        // Push the Docker image to DockerHub
                        sh "docker push $DOCKER_REGISTRY/$DOCKER_IMAGE"
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                // Deploy the app (e.g., using Docker)
                sh 'docker run -d -p 8080:8080 $DOCKER_REGISTRY/$DOCKER_IMAGE'
            }
        }
    }

    post {
        always {
            // Clean up any resources or run tasks after pipeline execution
            echo 'Pipeline execution completed.'
        }
        success {
            echo 'Pipeline executed successfully!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
