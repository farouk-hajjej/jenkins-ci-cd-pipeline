pipeline {
    agent any

    environment {
        NODE_VERSION = "node-20"
        registry = "faroukhajjej1/projet-devops-test"
        registryCredential = 'dckr_pat_VVIpmnatR2f0aNGVai7aTJ3TfSM'
        backendImage = ''
        frontendImage = ''
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

        stage('Install Backend Dependencies') {
            steps {
                dir('backend') {
                    echo 'Installing backend dependencies...'
                    sh 'npm ci'
                }
            }
        }

        stage('Install Frontend Dependencies') {
            steps {
                dir('frontend') {
                    echo 'Installing frontend dependencies...'
                    sh 'npm ci'
                }
            }
        }

        stage('Lint Code') {
            steps {
                dir('backend') {
                    sh 'npm run lint || true' // adapte si tu as ESLint configuré
                }
                dir('frontend') {
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

        stage('Initialize') {
            steps {
                script {
                    echo "Docker is expected to be installed and accessible via the Docker socket."
                    // Si besoin d'ajouter des paths ou outils manuellement, fais-le ici
                }
            }
        }

        stage('Check Docker') {
            steps {
                sh 'docker --version'
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
                    docker.withRegistry('', registryCredential) {
                        backendImage.push('latest')
                        frontendImage.push('latest')
                    }
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

        stage('Cleanup') {
            steps {
                echo 'Cleaning up...'
                sh 'docker-compose down'
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
