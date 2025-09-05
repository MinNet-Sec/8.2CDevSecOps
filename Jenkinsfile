pipeline {
  agent any

  stages {
    stage('Email Smoke Test') {
      steps {
        script {
          emailext(
            to: "sing.a.minahsong@gmail.com",
            from: "sing.a.minahsong@gmail.com",
            subject: "[Jenkins] Smoke Test",
            body: """이 메일은 emailext 동작 확인용입니다.
Build URL: ${env.BUILD_URL}""",
            mimeType: 'text/plain',
            attachLog: false
          )
        }
      }
    }
  }
}
