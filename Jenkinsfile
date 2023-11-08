@Library('github.com/devbyaccident/demo-shared-pipeline')

pipeline {
   agent any

   stages {
      stage('Call Library Function with an arguement') {
         steps {
            script {
               helloWorld()
            }
         }
      }
   }
}