pipeline {
  agent {
      docker {
        image 'nikolaik/python-nodejs'
      }
  }
  tools {nodejs "NODEJS"}
  stages {
    stage('Install sam-cli') {
      steps {
        sh 'python3 -m venv venv && venv/bin/pip install aws-sam-cli'
        stash includes: '**/venv/**/*', name: 'venv'
      }
    }
    stage('Build') {
      steps {
        unstash 'venv'
        sh 'apt install nodejs'
        sh 'venv/bin/sam build --beta-features'
        sh 'ls ./.aws-sam/build/HelloWorldFunction'
        stash includes: '**/.aws-sam/**/*', name: 'aws-sam'
      }
    }
    stage('beta') {
      environment {
        STACK_NAME = 'sam-jenkins-app'
        S3_BUCKET = 'jenkins-sam-lambda-deployment'
      }
      steps {
        withAWS(credentials: 'sam-jenkins-demo-credentials', region: 'ap-sount-1') {
          unstash 'venv'
          unstash 'aws-sam'
          sh 'venv/bin/sam deploy'
        }
      }
    }
  }
}