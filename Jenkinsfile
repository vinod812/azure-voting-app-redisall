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
            bat(script: 'docker compose build')
         }
      }
      stage('Start App') {
         steps {
            bat(script: 'docker compose up -d')
         }
      }
     /* stage('Run Tests') {
         steps {
            bat(script: 'pytest ./tests/test_sample.py')
         }
         post {
            success {
               echo "Tests passed! :)"
            }
            failure {
               echo "Tests failed :("
            }
         }
      }*/
      stage('Run Clair') {
         agent {label 'built-in'}
         steps {
            sh(script: 'docker run -p 5432:5432 -d --name db arminc/clair-db:latest')
            sh(script: 'docker run -p 6060:6060 --link db:postgres -d --name clair arminc/clair-local-scan:latest')
         }
      }
      stage('Run Clair scan') {
         agent {label 'built-in'}
         steps {
            sh(script: '/home/ubuntu/go/bin/clair-scanner --ip=172.17.0.1 blackdentech/jenkins-course:2023')
         }
      }
      stage('Docker Push') {
         steps {
            echo "Runnning in $WORKSPACE"
            dir("$WORKSPACE/azure-vote") {
               script {
                  docker.withRegistry('', 'vinod81212') {
                     def image = docker.build('vinod812/vinod812:tagname')
                     image.push()
                  }
               }
            }
         }
      }
   }
   post {
      always {
         bat(script: 'docker compose down')
      }
   }
}
