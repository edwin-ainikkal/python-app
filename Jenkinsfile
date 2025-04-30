pipeline {
    agent any

    environment {
        RUN_TESTS = "true"
        PYTHON_VERSION = "python3"
    }

    stages {
        stage('Checkout Code') {
            steps {
                echo "Checking out code from GitHub..."
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                echo "Installing dependencies..."
                sh '''#!/bin/bash
                    set -e &&
                    python3 -m venv venv &&
                    . venv/bin/activate &&
                    pip install --upgrade pip &&
                    pip install -r requirements.txt
                '''
            }
        }

        stage('Run Tests') {
            when {
                expression { return env.RUN_TESTS == "true" }
            }
            steps {
                echo "Running tests..."
                sh """
                    set -e
                    . venv/bin/activate
                    pytest --junitxml=reports/test-results.xml
                """
            }
        }

        stage('Run Application') {
            steps {
                echo "Starting Flask app for 60 seconds..."
                sh """
                    set -e
                    . venv/bin/activate
                    echo "Flask app will run for 60 seconds..."
                    timeout 60s python app.py || echo "App terminated after timeout, ignoring error"
                """
            }
        }
    }

    post {
        success {
            echo "✅ Pipeline executed successfully!"
        }
        failure {
            echo "❌ Pipeline failed!"
        }
    }
}
