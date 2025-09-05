pipeline {
  agent any
  tools { nodejs 'node24' }   // Make sure you have a NodeJS tool named "node24" in Jenkins

  environment {
    NOTIFY_TO = 'sing.a.minahsong@gmail.com'
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
          // Capture exit code without failing the whole build
          def testCode = bat(script: 'npm test', returnStatus: true)
          env.TEST_STATUS = (testCode == 0) ? 'SUCCESS' : 'FAILURE'
        }
      }
      post {
        always {
          emailext(
            to: "${env.NOTIFY_TO}",
            from: "sing.a.minahsong@gmail.com",
            subject: "[Jenkins] ${env.JOB_NAME} #${env.BUILD_NUMBER} - TEST stage ${env.TEST_STATUS}",
            body: """Hello,

The Test stage finished with status: ${env.TEST_STATUS}.
Job: ${env.JOB_NAME} #${env.BUILD_NUMBER}
Build URL: ${env.BUILD_URL}
Console Output: ${env.BUILD_URL}console

The console log is attached for details.
""",
            attachLog: true,
            compressLog: true,
            mimeType: 'text/plain'
          )
        }
      }
    }

    stage('Generate Coverage Report') {
      steps {
        bat 'npm run coverage || exit /b 0'
      }
    }

    stage('NPM Audit (Security Scan)') {
      steps {
        script {
          def auditCode = bat(script: 'npm audit', returnStatus: true)
          env.AUDIT_STATUS = (auditCode == 0) ? 'SUCCESS' : 'FAILURE'
        }
      }
      post {
        always {
          emailext(
            to: "${env.NOTIFY_TO}",
            from: "sing.a.minahsong@gmail.com",
            subject: "[Jenkins] ${env.JOB_NAME} #${env.BUILD_NUMBER} - SECURITY SCAN ${env.AUDIT_STATUS}",
            body: """Hello,

The Security Scan stage (npm audit) finished with status: ${env.AUDIT_STATUS}.
Job: ${env.JOB_NAME} #${env.BUILD_NUMBER}
Build URL: ${env.BUILD_URL}
Console Output: ${env.BUILD_URL}console

Note: The console log is attached for details.
""",
            attachLog: true,
            compressLog: true,
            mimeType: 'text/plain'
          )
        }
      }
    }
  }
}
