pipeline {
  agent any

  environment {
    DOCKER_CREDENTIALS_ID = 'ramya2907'
  }
  stages {
    stage('Clone Repository') {
      steps {
        git 'https://github.com/kattaramya/ci-cd-pipeline.git'
      }
    }
    stage('Build Docker Image') {
      steps {
        script {
          docker.build('ramya2907/yourimage:latest')
        }
      }
    }
    stage('Test Docker Image') {
      steps {
        script {
          docker.image('ramya2907/yourimage:latest').inside {
            sh 'echo "Running Tests..."'
            sh 'pytest' // Assuming you have tests
          }
        }
      }
    }
    stage('Push Docker Image') {
      steps {
        script {
          docker.withRegistry('https://index.docker.io/v1/', '165ca3a3-88be-4b2f-a142-acb8025af7dd') {
            docker.image('ramya2907/yourimage:latest').push('latest')
          }
        }
      }
    }
    stage('Deploy with Ansible') {
      steps {
        ansiblePlaybook(
          inventory: 'ansible/inventory',
          playbook: 'ansible/deploy.yml'
        )
      }
    }
  }
}
