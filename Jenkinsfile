pipeline {
    agent {
        docker {
            image 'python:3.11'
        }
    }

    stages {

        stage('Checkout') {
            steps {
                echo 'Pulling code from repository...'
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                sh '''
                apt update
                apt install -y python3 python3-pip
                pip3 install -r requirements.txt
                '''
            }
        }

        stage('Lint / Syntax Check') {
            steps {
                echo 'Checking Python syntax...'
                sh 'python -m py_compile app.py'
                echo 'Syntax OK!'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo 'Building Docker image...'
                sh 'docker build -t event-registration:latest .'
            }
        }

        stage('Deploy') {
            steps {
                echo 'Stopping old container if running...'
                sh 'docker stop event-registration || true'
                sh 'docker rm event-registration || true'

                echo 'Starting new container...'
                sh 'docker run -d --name event-registration -p 5000:5000 event-registration:latest'

                echo 'App deployed! Running at http://localhost:5000'
            }
        }
    }

    post {
        success {
            echo '✅ Pipeline completed successfully!'
        }
        failure {
            echo '❌ Pipeline failed. Check the logs above.'
        }
    }
}
