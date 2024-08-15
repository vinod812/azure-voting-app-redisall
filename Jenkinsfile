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
      /*stage('Run Tests') {
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
      stage('QA Deploy') {
         environment {
            KUBECONFIG = credentials('vv-kubeconfig')
         }
         when {
            branch 'feature/k8s-deploy'
         }
         steps {
            bat "kubectl apply -f azure-vote-all-in-one-redis.yaml --kubeconfig $KUBECONFIG"
         }
      }
     /* stage('Approve Deploy to PROD') {
         when {
            branch 'feature/k8s-deploy'
         }
         options {
            timeout(time: 1, unit: 'HOURS')
         }
         steps {
            input message: "Deploy to PROD?"
         }
      }
      stage('PROD Deploy') {
         environment {
            KUBECONFIG = credentials('prod-kubeconfig')
         }
         when {
            branch 'feature/k8s-deploy'
         }
         steps {
            sh "kubectl apply -f azure-vote-all-in-one-redis.yaml --kubeconfig $KUBECONFIG"
         }
      }*/
   }
   post {
      always {
         sh(script: 'docker compose down')
      }
   }
}
