pipeline {
  agent any
  environment {
    docker_username = 'nkorkiakoski'
  }
  stages {
    stage('Clone down') {
      agent{label 'master-label'}
        steps {
          stash(excludes: '.git', name: 'code')
      }
    }

    stage('Parallel execution') {
      parallel {
        stage('Say Hello') {
          steps {
            sh 'echo "hello world"'
          }
        }

        stage('Build app') {
          options {skipDefaultCheckout()}
          agent {
            docker {
              image 'gradle:jdk11'
            }

          }
          steps {
            unstash 'code'
            sh 'ci/build-app.sh'
            stash(excludes: '.git', name: 'code')
            archiveArtifacts 'app/build/libs/'
            sh 'ls'
            deleteDir()
            sh 'ls'
          }
        }

        stage('test app') {
          options {skipDefaultCheckout()}
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

    stage('component test') {
      when {
        anyOf {
          branch 'master'; changeRequest()
        }
      }
      steps {
        echo 'either master or pull request'
        sh 'ci/component-test.sh'
      }
    }

    stage('push docker app') {
      environment {
      DOCKERCREDS = credentials('docker_login') //use the credentials just created in this stage
      }
      when {
        branch 'master'
      }
      steps {
        unstash 'code' //unstash the repository code
        sh 'ci/build-docker.sh'
        sh 'echo "$DOCKERCREDS_PSW" | docker login -u "$DOCKERCREDS_USR" --password-stdin' //login to docker hub with the credentials above
        sh 'ci/push-docker.sh'
      }
    }

  }
}