pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/shristymukherjee31-design/nodejs-jenkins-ci.git'
            }
        }

        stage('Environment Check') {
            steps {
                sh '''
                    node --version
                    npm --version
                '''
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm ci'
            }
        }

        stage('Application Test') {
            steps {
                sh '''
                    node index.js > app.log 2>&1 &
                    PID=$!

                    sleep 2

                    echo "Testing Node.js application..."
                    curl -f http://localhost:3000/

                    STATUS=$?

                    kill $PID || true

                    exit $STATUS
                '''
            }
        }
    }

    post {
        success {
            echo 'Node.js CI Pipeline completed successfully.'
        }

        failure {
            echo 'Node.js CI Pipeline failed.'
        }

        always {
            echo 'Pipeline execution completed.'
        }
    }
}
