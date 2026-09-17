pipeline {
    agent none
    stages {
        stage('Test') {
            agent {
                docker { image 'python:3.12-slim' }
            }
            steps {
                sh '''
                    python -m venv venv
                    . venv/bin/activate
                    pip install --no-cache-dir -r requirements.txt
                    pytest tests/
                '''
            }
        }
        stage('Build Image') {
            agent any
            steps {
                sh 'docker build -t jenkins-test-app:$BUILD_NUMBER .'
            }
        }
        stage('Verify Image Runs') {
            agent any
            steps {
                sh '''
                    docker run -d --name test-run-$BUILD_NUMBER jenkins-test-app:$BUILD_NUMBER
                    sleep 3
                    CONTAINER_IP=$(docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' test-run-$BUILD_NUMBER)
                    curl -f http://$CONTAINER_IP:5000/health
                    docker stop test-run-$BUILD_NUMBER
                    docker rm test-run-$BUILD_NUMBER
                '''
            }
        }
    }
}
