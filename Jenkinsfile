@Library('github.com/devbyaccident/demo-shared-pipeline')

pipeline {
   agent any

   stages {
      stage('Verify Branch') {
         steps {
            script {
               helloWorld()
            }
         }
      }
   }
}