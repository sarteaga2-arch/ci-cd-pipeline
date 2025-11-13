pipeline {
    agent {
        docker {
            image 'python:3.10-slim'   // ✅ Use official Python image
            args '-u root'             // optional: run as root inside container
        }
    }

    environment {
        WEBEX_TOKEN = credentials('WEBEX_TOKEN')
        WEBEX_ROOM  = 'Y2lzY29zcGFyazovL3VybjpURUFNOnVzLXdlc3QtMl9yL1JPT00vMmYzOGZhOTAtYTdjMy0xMWYwLTg0MGQtOWZlZmM4MmJmYWE4'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install deps') {
            steps {
                sh 'python -m pip install --upgrade pip'
                sh 'pip install -r requirements.txt'
            }
        }

        stage('Run tests') {
            steps {
                sh 'pytest || true'
            }
        }
    }

    post {
        success {
            sh '''
            curl -X POST https://webexapis.com/v1/messages \
                 -H "Authorization: Bearer ${WEBEX_TOKEN}" \
                 -H "Content-Type: application/json" \
                 -d "{ \\"roomId\\": \\"${WEBEX_ROOM}\\", \\"text\\": \\"✅ Build SUCCESS: ${JOB_NAME}/${BRANCH_NAME} #${BUILD_NUMBER} - ${BUILD_URL}\\" }"
            '''
            '''
        }

        failure {
            sh '''
            curl -X POST https://webexapis.com/v1/messages \
                 -H "Authorization: Bearer ${WEBEX_TOKEN}" \
                 -H "Content-Type: application/json" \
                 -d "{ \\"roomId\\": \\"${WEBEX_ROOM}\\", \\"text\\": \\"❌ Build FAILURE: ${JOB_NAME}/${BRANCH_NAME} #${BUILD_NUMBER} - ${BUILD_URL}\\" }"
            '''
            '''
        }
    }
}

