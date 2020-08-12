pipeline {
  environment {
    docker_username = 'stephanienight'
  }
  agent any
  stages {
    stage('Clone Down') {
      steps {
        stash(name: 'code', excludes: '.git')
      }
    }

    stage('Parallel execution') {
      parallel {
      stage('Master branch build') {
          when { branch "master" }
          steps {
            sh 'Echo "On master branch"'
          }
        }

        stage('Build App') {
          agent {
            docker {
              image 'gradle:jdk11'
            }
          }
          steps {     
            unstash 'code'       
            sh 'ci/build-app.sh'
            stash(name: 'code', excludes: '.git')
          }
          options{
            skipDefaultCheckout()
          }
        }

        stage('Test app') {
          agent {
            docker {
              image 'gradle:jdk11'
            }

          }
          steps {
            unstash 'code'
            sh 'ci/unit-test-app.sh'
            junit 'app/build/test-results/test/TEST-*.xml'
          }
        }

      }
    }
    stage('Push to docker') {
      environment {
        DOCKERCREDS = credentials('docker_login') //use the credentials just created in this stage
      
      }
      steps {
        unstash 'code' //unstash the repository code
        sh 'ci/build-docker.sh'
        sh 'echo "$DOCKERCREDS_PSW" | docker login -u "$DOCKERCREDS_USR" --password-stdin' //login to docker hub with the credentials above
        sh 'ci/push-docker.sh'

      }
    }    
  }

post {
  always {
    deleteDir() /* clean up our workspace */
  }
}
}
