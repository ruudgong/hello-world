pipeline {
  agent any
  stages {
    stage('Build') {
      steps {
        echo 'Building..........'
        sleep 5
      }
    }

    stage('Test') {
      steps {
        echo 'Testing........'
        mail(subject: 'I\'m Testing', body: 'Hey Smart, I am  testing....', from: 'ruudgong', to: 'smart.nwachukwu@gmail.com')
      }
    }

    stage('Deploy') {
      steps {
        echo 'Deploying.......'
      }
    }

  }
}