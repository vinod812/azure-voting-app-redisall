@Library('github.com/devbyaccident/demo-shared-pipeline') _

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