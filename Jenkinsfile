pipeline {
    agent any

    options {
        timeout(time: 15, unit: 'MINUTES')
    }

    parameters {
        choice(
            name: 'DEPLOY_ENV',
            choices: ['dev', 'staging', 'prod'],
            description: 'Deployment environment'
        )
    }

    environment {
        APP_NAME = 'jenkins-python-demo'
    }

    stages {

        stage('Setup') {
            steps {
                sh '''
                    python3 -m venv venv
                    . venv/bin/activate
                    pip install --retries 5 --timeout 60 -r requirements.txt
                '''
            }
        }

        stage('Test') {
            steps {
                sh '''
                    . venv/bin/activate
                    mkdir -p reports
                    pytest -v --junitxml=reports/pytest.xml
                '''
            }
        }

        stage('Build') {
            steps {
                sh 'python3 -m compileall -q app.py'
            }
        }

        stage('Package') {
            steps {
                sh '''
                    mkdir -p dist
                    python3 -m zipfile -c dist/jenkins-python-demo.zip app.py
                '''
            }
        }

        stage('Deploy') {
            when {
                expression {
                    params.DEPLOY_ENV == 'prod'
                }
            }
            steps {
                echo "Deploying ${APP_NAME} to production"
            }
        }
    }

    post {
        always {
            junit testResults: 'reports/pytest.xml',
                  allowEmptyResults: true

            archiveArtifacts artifacts: 'dist/*.zip',
                             allowEmptyArchive: true
        }

        success {
            echo 'Pipeline completed successfully!'
        }

        failure {
            echo 'Pipeline failed!'
        }
    }
}
