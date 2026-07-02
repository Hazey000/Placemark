pipeline {
      agent any

      tools {
          nodejs 'Node22'
      }

      environment {
          cookie_name        = 'placemark_session'
          cookie_password    = credentials('cookie-password')
          db = credentials('mongo-test-uri')
          NODE_ENV           = 'test'
      }

      triggers {
          pollSCM('H/5 * * * *')
      }

      options {
          timeout(time: 10, unit: 'MINUTES')
          timestamps()
      }
  
      stages {
          stage('Install') {
              steps {
                  sh 'npm install'
              }
          }

          stage('Lint') {
              steps {
                  sh 'npm run lint'
              }
          }

          stage('Start MongoDB') {
              steps {
                  sh '''
                      mongod --fork --dbpath /tmp/jenkins-mongo --logpath /tmp/mongod.log
                      sleep 2
                  '''
              }
          }

          stage('BDD Test') {
              steps {
                  sh 'npm run testbdd'
              }
          }

           stage('Model Tests') {
          steps {
              sh 'npm run testmodels'
            }
        }
        stage('API Tests') {
            steps {
                sh 'npm run testapi'
            }
        }

      }

      post {
          always {
              sh 'mongod --shutdown --dbpath /tmp/jenkins-mongo || true'
              echo 'Pipeline complete.'
          }
          success {
              echo 'All tests passed.'
          }
          failure {
              echo 'Tests failed — check the console output.'
          }
      }
  }