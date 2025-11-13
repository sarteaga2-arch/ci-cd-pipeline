pipeline {

  agent any

  environment {

    // these credential IDs must exist in Jenkins Credentials

    WEBEX_TOKEN =  credentials('WEBEX_TOKEN') // secret text

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
        sh '''
            python3 -m venv venv
            . venv/bin/activate
            python -m pip install --upgrade pip
            pip install -r requirements.txt
        '''
      }

    }

    stage('Run tests') {

      steps {

        sh './venv/bin/pytest -q --junitxml=results.xml'

      }

      post {

        always {

          junit 'results.xml'

        }

      }

    }

  }

  post {
    always {
        script {
            sh '''
            curl -X POST https://webexapis.com/v1/messages \
                -H "Authorization: Bearer ${WEBEX_TOKEN}" \
                -H "Content-Type: application/json" \
                -d '{
                    "roomId": "'${WEBEX_ROOM}'",
                    "text": "Build ${JOB_NAME} #${BUILD_NUMBER} finished. Status: ${currentBuild.currentResult}"
                }'
            '''
        }
    }
}

}


