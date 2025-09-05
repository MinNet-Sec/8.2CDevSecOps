pipeline {
  agent any
  tools { nodejs 'node24' }   // <-- your Tools name (node20/node24)

  environment {
    NOTIFY_TO = 'your.email@example.com'   // <-- change to your address
  }

  stages {
    stage('Checkout') {
      steps {
        git branch: 'main', url: 'https://github.com/MinNet-Sec/8.2CDevSecOps.git'
      }
    }

    stage('Install Dependencies') {
      steps {
        bat 'npm install'
      }
    }

    stage('Run Tests') {
      steps {
        script {
          // Capture exit code instead of failing the build
          def testCode = bat(script: 'npm test', returnStatus: true)
          env.TEST_STATUS = (testCode == 0) ? 'SUCCESS' : 'FAILURE'
        }
      }
      post {
        always {
          emailext(
            to: "${env.NOTIFY_TO}",
            subject: "[Jenkins] ${env.JOB_NAME} #${env.BUILD_NUMBER} - TEST stage ${env.TEST_STATUS}",
            body: """Hello,
Test stage finished with status: ${env.TEST_STATUS}
Job: ${env.JOB_NAME} #${env.BUILD_NUMBER}
Build URL: ${env.BUILD_URL}
Console: ${env.BUILD_URL}console
""",
            attachLog: true,
            compressLog: true
          )
        }
      }
    }

    stage('NPM Audit (Security Scan)') {
      steps {
        script {
          // Capture audit exit code too (0 means no vulns/high level issues)
          def auditCode = bat(script: 'npm audit', returnStatus: true)
          env.AUDIT_STATUS = (auditCode == 0) ? 'SUCCESS' : 'FAILURE'
        }
      }
      post {
        always {
          emailext(
            to: "${env.NOTIFY_TO}",
            subject: "[Jenkins] ${env.JOB_NAME} #${env.BUILD_NUMBER} - SECURITY SCAN ${env.AUDIT_STATUS}",
            body: """Hello,
Security Scan (npm audit) finished with status: ${env.AUDIT_STATUS}
Job: ${env.JOB_NAME} #${env.BUILD_NUMBER}
Build URL: ${env.BUILD_URL}
Console: ${env.BUILD_URL}console

Note: The console log is attached for details.
""",
            attachLog: true,
            compressLog: true
          )
        }
      }
    }
  }
}
