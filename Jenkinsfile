pipeline {
  agent any
  tools { nodejs 'node24' }

  environment {
    NOTIFY_TO = 'sing.a.minahsong@gmail.com'
  }

  stages {
    // 🔸 임시: SMTP 동작 확인용 스모크 테스트 메일
    stage('Email Smoke Test') {
      steps {
        script {
          emailext(
            to: "${env.NOTIFY_TO}",
            subject: "[Jenkins] Email smoke test for ${env.JOB_NAME} #${env.BUILD_NUMBER}",
            body: """안녕하세요,

이 메일은 Jenkins에서 Gmail SMTP 설정이 정상적으로 동작하는지 확인하기 위한 테스트 메일입니다.
만약 이 메일을 받았다면 설정이 성공한 것입니다.

Build URL: ${env.BUILD_URL}
""",
            mimeType: 'text/plain',
            attachLog: false
          )
        }
      }
    }

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
            compressLog: true,
            mimeType: 'text/plain'
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
            compressLog: true,
            mimeType: 'text/plain'
          )
        }
      }
    }
  }
}
