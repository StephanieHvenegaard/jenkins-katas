pipeline {
  agent any
  stages {
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
            sh 'ci/build-app.sh'
          }
        }

        stage('Test app') {
          steps {
            sh 'ci/unit-test-app.sh'
          }
        }

      }
    }

    stage('Clone Down') {
      agent {
        node {
          label 'host'
        }

      }
      steps {
        stash(name: 'Code', excludes: '.git')
      }
    }

  }
}