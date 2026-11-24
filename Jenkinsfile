pipeline {
  agent any
  options { timestamps() }

  environment {
    REPO = 'intissarsagaama'
    TAG  = "build-${env.BUILD_NUMBER}"
  }

  stages {
    stage('Checkout') {
      steps { checkout scm }
    }

    stage('Build images') {
      steps {
        bat 'docker version'
        bat "docker build -t %REPO%/mern-server:%TAG% -t %REPO%/mern-server:latest ./Server"
        bat "docker build -t %REPO%/mern-client:%TAG% -t %REPO%/mern-client:latest ./Client"
      }
    }

    stage('Deploy (docker compose)') {
      steps {
        bat 'docker rm -f mongodb server client 2>nul || ver > nul'
        bat 'docker compose up -d --build'
        bat 'docker compose ps'
      }
    }

    stage('Smoke Test') {
      steps {
        bat 'ping -n 11 127.0.0.1 > nul'
        bat 'curl -s http://localhost:9000/api/health | find "API is running"'
        bat 'curl -I http://localhost:3000 | find "200 OK"'
      }
    }

    stage('Push (Docker Hub)') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockerhub-creds',
                                          usernameVariable: 'USER',
                                          passwordVariable: 'PASS')]) {
          bat 'echo %PASS% | docker login -u %USER% --password-stdin'
          bat "docker push %REPO%/mern-server:%TAG%"
          bat "docker push %REPO%/mern-server:latest"
          bat "docker push %REPO%/mern-client:%TAG%"
          bat "docker push %REPO%/mern-client:latest"
        }
      }
    }
  }

  post {
    success { echo 'TP4 : Build + Deploy + Test + Push OK' }
    failure { echo 'TP4 : pipeline KO' }
  }
}
