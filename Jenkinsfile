pipeline {
    agent any
    stages {
        stage('Test') {
            steps {
                sh '''
                    python3 -m venv venv
                    . venv/bin/activate
                    pip install -r requirements.txt
                    pytest tests/
                '''
            }
        }
        stage('Build Image') {
            steps {
                sh 'docker build -t jenkins-test-app:$BUILD_NUMBER .'
            }
        }
        stage('Verify Image Runs') {
            steps {
                sh '''
                    docker run -d --name test-run-$BUILD_NUMBER -p 5001:5000 jenkins-test-app:$BUILD_NUMBER
                    sleep 3
                    curl -f http://localhost:5001/health
                    docker stop test-run-$BUILD_NUMBER
                    docker rm test-run-$BUILD_NUMBER
                '''
            }
        }
    }
}
