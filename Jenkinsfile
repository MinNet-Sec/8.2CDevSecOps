pipeline {
  agent any

  stages {
    stage('Email Smoke Test') {
      steps {
        script {
          emailext(
            to: "sing.a.minahsong@gmail.com",
            from: "sing.a.minahsong@gmail.com",
            subject: "Jenkins 최소 테스트 메일",
            body: "이건 본문만 있는 간단한 테스트 메일입니다.",
            mimeType: 'text/plain',
            attachLog: false
          )
        }
      }
    }
  }
}
