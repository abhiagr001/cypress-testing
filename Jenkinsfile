pipeline {
    agent any

    options {
        // Keep last 30 builds
        buildDiscarder(logRotator(numToKeepStr: '30'))
        // Timeout after 1 hour
        timeout(time: 1, unit: 'HOURS')
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
                                echo 'Installing dependencies (using Node tool or fallback)...'
                                sh '''#!/bin/bash -l
set -euo pipefail
# prefer tool-provided npm on PATH, otherwise fall back to common Homebrew location
NPM_CMD="$(command -v npm || true)"
if [ -z "$NPM_CMD" ]; then
    if [ -x "/opt/homebrew/bin/npm" ]; then
        NPM_CMD="/opt/homebrew/bin/npm"
    elif [ -x "/usr/local/bin/npm" ]; then
        NPM_CMD="/usr/local/bin/npm"
    else
        echo "npm not found in PATH or common locations"
        exit 1
    fi
fi
echo "Using npm: $NPM_CMD"
"$NPM_CMD" ci || "$NPM_CMD" install
'''
                        }
                }

                stage('Run Cypress Tests') {
                        steps {
                                echo 'Running Cypress tests...'
                                sh '''#!/bin/bash -l
set -euo pipefail
NPM_CMD="$(command -v npm || true)"
if [ -z "$NPM_CMD" ]; then
    if [ -x "/opt/homebrew/bin/npm" ]; then
        NPM_CMD="/opt/homebrew/bin/npm"
    elif [ -x "/usr/local/bin/npm" ]; then
        NPM_CMD="/usr/local/bin/npm"
    else
        echo "npm not found; aborting"
        exit 1
    fi
fi
# run Cypress via npx so it uses project-local binary when available
"$NPM_CMD" exec -- npx --yes cypress run
'''
                        }
                }

                stage('Archive Results') {
                        when {
                                anyOf { expression { return true } }
                        }
                        steps {
                                echo 'Archiving Cypress artifacts (screenshots/videos)'
                                archiveArtifacts allowEmptyArchive: true, artifacts: 'cypress/screenshots/**, cypress/videos/**, cypress/results/**, junit-reports/**'
                        }
                }

        stage('Generate Report') {
            steps {
                echo 'Test execution completed'
            }
        }
    }

    post {
        always {
            // Clean up workspace
            cleanWs()
        }
        success {
            echo 'Pipeline succeeded!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
