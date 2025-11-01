pipeline {
  agent any

  options {
    timestamps()
    buildDiscarder(logRotator(numToKeepStr: '3'))
    disableConcurrentBuilds()
  }

  tools {
    jdk 'jdk17'
    maven 'maven-3.9'
  }

  environment {
    BUILD_GATE = "${env.BUILD_NUMBER}"
  }

  stages {
    stage('Checkout') {
      steps { checkout scm }
    }

    stage('Build & Test') {
      steps {
        sh 'mvn -B -q clean test package'
        archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
        junit 'target/surefire-reports/*.xml'
      }
    }

    stage('Build Gate (<5 fails)') {
      steps {
        script {
          int n = BUILD_GATE as Integer
          if (n < 5) { error "Gate: BUILD_NUMBER ${n} < 5 → فشل متعمد قبل الإطلاق" }
        }
      }
    }
  }

  post {
    success { echo 'S: ✅ success' }
    failure { echo 'F: ❌ failed' }
    always  { cleanWs() }
  }
}
