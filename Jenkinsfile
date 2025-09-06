pipeline {
  agent any
  environment {
    NODE_ENV = 'test'
  }
  parameters {
    string(name: 'BRANCH', defaultValue: 'main', description: 'Branch to build')
  }
  triggers {
    // Poll SCM as fallback (if webhooks not configured)
    pollSCM('H/5 * * * *')
  }
  stages {
    stage('Checkout') {
      steps {
        checkout scm: [$class: 'GitSCM', branches: [[name: "*/${params.BRANCH}"]], userRemoteConfigs: [[url: 'https://github.com/you/your-repo.git']]]
      }
    }
    stage('Install') {
      steps {
        sh 'npm ci'
      }
    }
    stage('Test') {
      steps {
        sh 'npm test'
      }
      post {
        always {
          junit 'test-results/*.xml'    // if tests produce JUnit XML
        }
      }
    }
    stage('Build') {
      steps {
        sh 'npm run build'
        stash includes: 'build/**', name: 'built'
      }
    }
    stage('Archive') {
      steps {
        unstash 'built'
        archiveArtifacts artifacts: 'build/**', fingerprint: true
      }
    }
  }
  post {
    success { echo "Build succeeded" }
    failure { mail to: 'you@example.com', subject: "Build failed", body: "Check Jenkins" }
  }
}
