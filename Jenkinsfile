pipeline {
    agent any

    environment {
        NODE_VERSION = "node-20"
        registry = "faroukhajjej1/projet-devops-test"
        registryCredential = 'dockerhub-credentials'
    }

    tools {
        nodejs "${NODE_VERSION}"
    }

    stages {
        stage('Checkout') {
            steps {
                echo 'Cloning repository...'
                git branch: 'main', url: 'https://github.com/farouk-hajjej/jenkins-ci-cd-pipeline.git'
            }
        }

        stage('Show Date') {
            steps {
                script {
                    def date = new Date()
                    def sdf = new java.text.SimpleDateFormat("MM/dd/yyyy HH:mm:ss")
                    echo "Build Date: ${sdf.format(date)}"
                }
            }
        }

        stage('Lint Code') {
            steps {
                dir('backend') {
                    sh 'npm install'
                    sh 'npm run lint || true'
                }
                dir('frontend') {
                    sh 'npm install'
                    sh 'npm run lint || true'
                }
            }
        }

        stage('Security Scan (npm audit)') {
            steps {
                dir('backend') {
                    sh 'npm audit --audit-level=high || true'
                }
                dir('frontend') {
                    sh 'npm audit --audit-level=high || true'
                }
            }
        }

        stage('Build Docker Images') {
            steps {
                script {
                    echo 'Building Docker images...'
                    backendImage = docker.build("${registry}-backend", "backend")
                    frontendImage = docker.build("${registry}-frontend", "frontend")
                }
            }
        }

        stage('Push Docker Images') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', registryCredential) {
                        backendImage.push('dev')
                        frontendImage.push('dev')
                    }
                }
            }
        }

        stage('Scan Docker Images (Grype)') {
            steps {
                script {
                    echo 'Scanning Docker images with Grype...'
                    sh """
                        docker pull ${registry}-backend:dev
                        docker pull ${registry}-frontend:dev
                        docker run --rm -v /var/run/docker.sock:/var/run/docker.sock anchore/grype:latest ${registry}-backend:dev --fail-on high || true
                        docker run --rm -v /var/run/docker.sock:/var/run/docker.sock anchore/grype:latest ${registry}-frontend:dev --fail-on high || true
                    """
                }
            }
        }

        stage('Run with docker-compose') {
            steps {
                echo 'Starting services with docker-compose...'
                sh 'docker-compose up -d --build'
            }
        }
    }

    post {
        always {
            echo 'Pipeline finished.'
        }
        success {
            echo 'Pipeline succeeded.'
        }
        failure {
            echo 'Pipeline failed.'
        }
    }
}
