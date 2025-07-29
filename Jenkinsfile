pipeline {
    agent any

    environment {
        NODE_VERSION = "node-20"
        registry = "faroukhajjej1/projet-devops-test"
        registryCredential = 'dockerhub-credentials' // 🔐 DockerHub credentials ID
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

        stage('Date') {
            steps {
                script {
                    def date = new Date()
                    def sdf = new java.text.SimpleDateFormat("MM/dd/yyyy")
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
                echo 'Building Docker images...'
                script {
                    backendImage = docker.build("${registry}-backend", "./backend")
                    frontendImage = docker.build("${registry}-frontend", "./frontend")
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

        stage('Test Backend (optional)') {
            when {
                expression { fileExists('backend/package.json') }
            }
            steps {
                dir('backend') {
                    script {
                        if (sh(script: "npm run test", returnStatus: true) != 0) {
                            error("Tests failed")
                        }
                    }
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline finished.'
        }
        failure {
            echo 'Pipeline failed.'
        }
        success {
            echo 'Pipeline succeeded.'
        }
    }
}
