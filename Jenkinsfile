pipeline {
    agent any

    tools {
        nodejs 'NodeJS'
    }

    parameters {
        string(name: 'BRANCH_NAME', defaultValue: 'main', description: 'Branch to build from')
        string(name: 'STUDENT_NAME', defaultValue: 'Nabeeha Islam', description: 'Provide your name here, no name = no marks')
        choice(name: 'ENVIRONMENT', choices: ['dev', 'qa', 'prod'], description: 'Select environment')
        booleanParam(name: 'RUN_TESTS', defaultValue: true, description: 'Run Jest tests after build')
    }

    environment {
        APP_VERSION = "1.0.${BUILD_NUMBER}"
        MAINTAINER = "Student"
    }

    stages {
        stage('Checkout') {
            steps {
                echo "Checking out branch: ${params.BRANCH_NAME}"
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                echo "Installing required packages..."
                bat 'npm install'
            }
        }

        stage('Build') {
            steps {
                echo "Building version ${APP_VERSION} for ${params.ENVIRONMENT} environment"
                bat '''
                    echo Simulating build process...
                    if not exist build mkdir build
                    copy src\\*.js build\\
                    echo Build completed successfully!
                    echo App version: %APP_VERSION% > build\\version.txt
                '''
            }
        }

        stage('Test') {
            when {
                expression { return params.RUN_TESTS }
            }
            steps {
                echo "Running Jest tests..."
                bat 'npm test'
            }
        }

        stage('Package') {
            steps {
                echo "Creating zip archive for version ${APP_VERSION}"
                // ✅ Correctly invoke PowerShell
                bat '''
                    powershell -NoProfile -ExecutionPolicy Bypass -Command ^
                    "try {
                        Write-Host 'Importing PowerShell Archive module...';
                        Import-Module Microsoft.PowerShell.Archive -Force;
                        Write-Host 'Compressing build folder...';
                        Compress-Archive -Path 'build\\*' -DestinationPath 'build_${env:APP_VERSION}.zip' -Force;
                        Write-Host 'Archive created successfully: build_${env:APP_VERSION}.zip';
                    } catch {
                        Write-Host 'Compression failed:' $_;
                        exit 1;
                    }"
                '''
            }
        }

        stage('Deploy (Simulation)') {
            steps {
                echo "Simulating deployment of version ${APP_VERSION} to ${params.ENVIRONMENT}"
                bat '''
                    echo Deploying build_${APP_VERSION}.zip to ${params.ENVIRONMENT} environment...
                    echo Deployment simulated successfully.
                '''
            }
        }
    }

    post {
        always {
            echo "Cleaning up workspace..."
            deleteDir()
        }
        success {
            echo "Pipeline succeeded! Version ${APP_VERSION} built and tested."
        }
        failure {
            echo "Pipeline failed! Check console output for details."
        }
    }
}
