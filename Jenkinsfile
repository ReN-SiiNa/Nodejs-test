pipeline {
    agent any
    tools {
        nodejs 'nodejs'
    }
 
    environment {
        NODEJS_HOME = 'C:/Program Files/nodejs'
        SONAR_SCANNER_PATH = 'C:/Users/rensi/Downloads/sonar-scanner-cli-6.2.1.4610-windows-x64/sonar-scanner-6.2.1.4610-windows-x64/bin'
    }
 
    stages {
        stage('Checkout') {
            steps {
                echo 'Checking out the source code...'
                checkout scm
            }
        }
 
        stage('Install Dependencies') {
            steps {
                echo 'Installing dependencies...'
                bat '''
                set PATH=%NODEJS_HOME%;%PATH%
                npm install
                '''
            }
        }
 
        stage('Lint') {
            steps {
                echo 'Running linting to ensure code quality...'
                bat '''
                set PATH=%NODEJS_HOME%;%PATH%
                npm run lint || exit /b 1
                '''
            }
        }
 
        stage('Build') {
            steps {
                echo 'Building the React application...'
                bat '''
                set PATH=%NODEJS_HOME%;%PATH%
                npm run build || exit /b 1
                '''
            }
        }
 
        stage('SonarQube Analysis') {
            environment {
                SONAR_TOKEN = credentials('sonar-token') // Access the SonarQube token stored in Jenkins credentials
            }
            steps {
                echo 'Performing SonarQube analysis...'
                bat '''
                set PATH=%SONAR_SCANNER_PATH%;%PATH%
                where sonar-scanner || echo "SonarQube scanner not found. Please install it."
                sonar-scanner -Dsonar.projectKey=backend_sonar ^
                    -Dsonar.sources=. ^
                    -Dsonar.host.url=http://localhost:9000 ^
                    -Dsonar.token=%SONAR_TOKEN% || exit /b 1
                '''
            }
        }
    }
 
    post {
        success {
            echo 'Pipeline completed successfully.'
        }
        failure {
            echo 'Pipeline failed. Please check the logs for more details.'
        }
        always {
            echo 'This runs regardless of the result.'
            cleanWs() // Cleanup the workspace after the pipeline
        }
    }
}
