
pipeline {
    agent any

    environment {
        APP_NAME = 'jenkins-python-demo'
    }

    stages {

        stage('Checkout') {
            steps {
                echo 'Checking out source code'
                git branch: 'main',
                    url: 'https://github.com/14jasimmtp/jenkins-python-demo.git'
            }
        }

        stage('Setup') {
            steps {
                echo 'Installing dependencies'

                sh '''
                    python3 -m venv venv
                    . venv/bin/activate

                    pip install \
                        --retries 5 \
                        --timeout 60 \
                        -r requirements.txt
                '''
            }
        }

        stage('Test') {
            steps {
                echo 'Running unit tests'
                sh '''
                    . venv/bin/activate
                    pytest -v
                '''
            }
        }

        stage('Build') {
            steps {
                echo 'Compiling Python files'
                sh '''
                    python3 -m compileall -q .
                '''
            }
        }

        stage('Run') {
            steps {
                echo 'Running application'
                sh '''
                    . venv/bin/activate
                    python app.py
                '''
            }
        }
    }

    post {
        success {
            echo 'Python pipeline completed successfully!'
        }

        failure {
            echo 'Python pipeline failed!'
        }

        always {
            echo 'Pipeline execution finished'
        }
    }
}
