pipeline {
  agent any

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Install') {
      steps {
        sh 'npm ci'
      }
    }

    stage('Build') {
      steps {
        sh 'npm run build'
      }
    }
    stage('test') {
      steps {
        sh 'npm test'
      }
    }
    stage("Integration Test"){
      steps{
        // Start the API server in background
            sh 'nohup node src/app.js >/tmp/app.log 2>&1 & sleep 2'

            // Run Postman tests using Newman
            sh 'newman run Todo.postman_collection.json'
      }
    }
    stage('Performance Test') {
        steps {
          // Run K6 performance test
          sh 'k6 run performance-test.js'
        }
      }
      stage('Security Test - SonarQube') {
          environment {
              SONAR_TOKEN = credentials('SONAR_TOKEN')
            }
          steps {
            sh '''
              sonar-scanner \
                -Dsonar.projectKey=ci-cd-pipeline \
                -Dsonar.sources=./src \
                -Dsonar.host.url=http://3.239.59.204:9000 \
                -Dsonar.login=$SONAR_TOKEN
            '''
          }
        }
    //Low code
  }
}
