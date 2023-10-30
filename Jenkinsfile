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
      stage('Run Grype') {
         agent {label 'build-1'}
         steps {
            grypeScan autoInstall: false, repName: 'grypeReport_bitnami_${JOB_NAME}_${BUILD_NUMBER}.txt', scanDest: 'registry:mcr.microsoft.com/oss/bitnami/redis:6.0.8'
            grypeScan autoInstall: false, repName: 'grypeReport_front_${JOB_NAME}_${BUILD_NUMBER}.txt', scanDest: 'registry:mcr.microsoft.com/azuredocs/azure-vote-front:v1'
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
   post {
      always {
         sh(script: 'docker compose down')
      }
   }
}
