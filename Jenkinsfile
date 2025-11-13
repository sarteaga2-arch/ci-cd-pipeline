pipeline {

  agent any

  environment {

    // these credential IDs must exist in Jenkins Credentials

    WEBEX_TOKEN =  'NTc2ZDA0YzgtMzJlMS00OGZjLTgzZDctYzk2YWUyNjMyOGY0YTU5MTc3NWItODNj_P0A1_bac71010-7484-48b3-b7ce-a267353178c5' // secret text

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


