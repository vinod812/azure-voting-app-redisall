pipeline {
   agent any
   stages {
      stage('Run Golang') {
         steps {
            sh(script: 'go version')
         }

         agent {
            docker {
               image 'golang:latest'
            }
         }
      }
      stage('Fail Golang') {
         steps {
            sh(script: 'go version')
         }
      }
   }
}