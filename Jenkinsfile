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
