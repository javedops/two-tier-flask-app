@Library('Shared') _

pipeline {

    agent { label 'dev' }

    triggers {
        githubPush()
    }

    options {
        buildDiscarder(logRotator(numToKeepStr: '10'))
        timestamps()
    }

    stages {

        stage('Code Clone') {
            steps {
                script {
                    clone('https://github.com/javedops/two-tier-flask-app.git', 'main')
                }
            }
        }

        stage('Trivy File System Scan') {
            steps {
                script {
                    trivy_fs()
                }
            }
        }

        stage('Build') {
            steps {
                sh 'docker build -t two-tier-flask-app .'
            }
        }

        stage('Test') {
            steps {
                echo 'Add your tests here.'
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    // args: docker hub user, image name, tag, jenkins credential ID
                    docker_push('javedops', 'two-tier-flask-app', 'latest', 'DockerHubCreads')
                }
            }
        }

        stage('Deploy') {
            steps {
                // docker-compose.yml must use: image: javedops/two-tier-flask-app:latest
                sh 'docker compose pull flask-app'
                sh 'docker compose up -d flask-app'
            }
        }
    }

    post {
        always {
            sh 'docker image prune -f'
        }
        success {
            emailext(
                from: 'your-email@example.com',
                to: 'your-email@example.com',
                subject: 'Build success for Demo CICD App',
                body: "Build #${env.BUILD_NUMBER} succeeded.\n${env.BUILD_URL}"
            )
        }
        failure {
            emailext(
                from: 'your-email@example.com',
                to: 'your-email@example.com',
                subject: 'Build failed for Demo CICD App',
                body: "Build #${env.BUILD_NUMBER} failed.\n${env.BUILD_URL}console"
            )
        }
    }
}
