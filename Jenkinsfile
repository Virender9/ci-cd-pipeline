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
    stage('Test') {
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
              sudo docker run --rm \
                -v $(pwd):/usr/src \
                sonarsource/sonar-scanner-cli \
                sonar-scanner \
                  -Dsonar.projectKey=ci-cd-pipeline \
                  -Dsonar.sources=/usr/src/src \
                  -Dsonar.host.url=http://<EC2-Public-IP>:9000 \
                  -Dsonar.login=$SONAR_TOKEN
            '''
          }
        }

        stage('Deploy to Staging') {
            steps {
              sh '''
                # Stop old container if running
                docker rm -f staging-app || true

                # Build new image
                docker build -t staging-app .

                # Run container on port 3000
                docker run -d --name staging-app -p 3000:3000 staging-app
              '''
            }
          }
    //Low code
  }
}
