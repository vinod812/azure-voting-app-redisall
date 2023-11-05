pipeline {
   agent any

   stages {
      stage('Verify Branch') {
         steps {
            echo "$GIT_BRANCH"
         }
      }
      stage('Docker Build') {
         steps {
            sh(script: 'docker compose build')
         }
      }
      stage('Start App') {
         steps {
            sh(script: 'docker compose up -d')
         }
      }
      stage('Run Tests') {
         steps {
            sh(script: 'pytest ./tests/test_sample.py')
         }
         post {
            success {
               echo "Tests passed! :)"
            }
            failure {
               echo "Tests failed :("
            }
         }
      }
      stage('Run Clair') {
         agent {label 'build-2'}
         steps {
            sh(script: 'docker run -p 5432:5432 -d --name db arminc/clair-db:latest')
            sh(script: 'docker run -p 6060:6060 --link db:postgres -d --name clair arminc/clair-local-scan:latest')
         }
      }
      stage('Container Scanning') {
         parallel {
            stage('Run Clair scan') {
               agent {label 'build-2'}
               steps {
                  //sh(script: '/home/ubuntu/go/bin/clair-scanner --ip=172.17.0.1 blackdentech/jenkins-course:2023')
                  sleep time: 1, unit: 'MINUTES'
               }
            }
            stage('Run Grype') {
               agent {label 'build-1'}
               steps {
                  grypeScan autoInstall: false, repName: 'grypeReport_${JOB_NAME}_${BUILD_NUMBER}.txt', scanDest: 'registry:blackdentech/jenkins-course:2023'
               }
               post {
                  always {
                     recordIssues(
                        tools: [grype()],
                        aggregatingResults: true,
                     )
                  }
               }
            }
         }
      }
   }
   post {
      always {
         sh(script: 'docker compose down')
         sh(script: 'docker stop $(docker ps -aq)')
         sh(script: 'docker rm $(docker ps -aq)')
      }
   }
}
