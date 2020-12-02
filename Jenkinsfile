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
            }

          }
          steps {
            unstash 'code'
            sh 'ci/build-app.sh'
            archiveArtifacts 'app/build/libs/'
            sh 'ls'
            deleteDir()
            sh 'ls'
            sh 'skipDefaultCheckout(true)'
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