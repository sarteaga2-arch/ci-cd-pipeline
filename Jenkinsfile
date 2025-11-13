pipeline {
  agent any
  environment {
    // these credential IDs must exist in Jenkins Credentials
    WEBEX_TOKEN = credentials('WEBEX_TOKEN')  // secret text
    WEBEX_ROOM  = credentials('WEBEX_ROOM')   // secret text (roomId)
  }
  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }
    stage('Install deps') {
      steps {
        sh 'python -m pip install --upgrade pip || true'
        sh 'pip install -r requirements.txt'
      }
    }
    stage('Run tests') {
      steps {
        sh 'pytest -q --junitxml=results.xml'
      }
      post {
        always {
          junit 'results.xml'
        }
      }
    }
  }
  post {
    success {
      script {
        def msg = "✅ Build SUCCESS: ${env.JOB_NAME} #${env.BUILD_NUMBER} - ${env.BUILD_URL}"
        sh """
          curl -X POST https://webexapis.com/v1/messages \
            -H "Authorization: Bearer ${WEBEX_TOKEN}" \
            -H "Content-Type: application/json" \
            -d '{ "roomId": "${WEBEX_ROOM}", "text": "${msg}" }'
        """
      }
    }
    failure {
      script {
        def msg = "❌ Build FAILURE: ${env.JOB_NAME} #${env.BUILD_NUMBER} - ${env.BUILD_URL}"
        sh """
          curl -X POST https://webexapis.com/v1/messages \
            -H "Authorization: Bearer ${WEBEX_TOKEN}" \
            -H "Content-Type: application/json" \
            -d '{ "roomId": "${WEBEX_ROOM}", "text": "${msg}" }'
        """
      }
    }
  }
}
