pipeline {
  agent any
  stages {
    stage('Clone Down') {
      steps {
        stash(name: 'Code', excludes: '.git')
      }
    }

    stage('Parallel execution') {
      parallel {
        stage('Say hello') {
          steps {
            sh 'echo Hello world i wanna die.'
          }
        }

        stage('Build App') {
          agent {
            docker {
              image 'gradle:jdk11'
            }

          }
          steps {
            skipDefaultCheckout true
            sh 'ci/build-app.sh'
          }
        }

        stage('Test app') {
          steps {
            unstash 'code'
            sh 'ci/unit-test-app.sh'
          }
        }

      }
    }

  }
}