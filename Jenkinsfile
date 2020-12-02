pipeline {
  agent any
  stages {
    stage('Parallel execution') {
      parallel {
        stage('Say Hello') {
          steps {
            sh 'echo "hello world"'
          }
        }

        stage('Build app') {
          agent {
            docker {
              image 'gradle:jdk11'
              args 'skipDefaultCheckout(true)'
            }

          }
          steps {
            unstash 'code'
            sh 'ci/build-app.sh'
            archiveArtifacts 'app/build/libs/'
            sh 'ls'
            deleteDir()
            sh 'ls'
          }
        }

      }
    }

    stage('Clone down') {
      agent {
        node {
          label 'host'
        }

      }
      steps {
        stash(excludes: '.git', name: 'code')
      }
    }

  }
}