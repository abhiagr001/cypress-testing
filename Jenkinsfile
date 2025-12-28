pipeline {
    agent any

    options {
        buildDiscarder(logRotator(numToKeepStr: '30'))
        timeout(time: 1, unit: 'HOURS')
        timestamps()  // Add timestamps to logs
    }

    environment {
        NODE_ENV = 'test'
    }

    tools {
        nodejs 'Node'
    }

    stages {
        stage('Checkout') {
            steps {
                echo 'Checking out code...'
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                echo 'Installing dependencies...'
                sh '''#!/bin/bash -e
# Use Jenkins tool or fallback to Homebrew
NPM_CMD="${NPM_BIN:-npm}"
$NPM_CMD ci || $NPM_CMD install
'''
            }
        }

        stage('Run Cypress Tests') {
            steps {
                withCredentials([string(credentialsId: 'cypress-record-key', variable: 'CYPRESS_RECORD_KEY')]) {
                    echo 'Running Cypress tests with recording...'
                    sh '''#!/bin/bash -e
NPM_CMD="${NPM_BIN:-npm}"
$NPM_CMD exec -- npx --yes cypress run --record --key "$CYPRESS_RECORD_KEY"
'''
                }
            }
        }

        stage('Archive Results') {
            when { always() }
            steps {
                echo 'Archiving Cypress artifacts...'
                archiveArtifacts allowEmptyArchive: true, 
                    artifacts: 'cypress/screenshots/**, cypress/videos/**, cypress/results/**'
            }
        }
    }

    post {
        always {
            echo 'Cleaning workspace...'
            cleanWs()
        }
        success {
            echo '✓ Pipeline succeeded!'
        }
        failure {
            echo '✗ Pipeline failed! Check logs and artifacts.'
        }
    }
}