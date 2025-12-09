pipeline{
    agaent any

    triggers{
        githubPush()
    }

    stages{
        stage("Checkout code"){
            steps{
                checkout scm // to fetch the code from github
            }
        }
        stage("Install"){
            steps{
                sh 'npm ci'
            }
        }
        stage("Build"){
            steps{
                sh "npm run build"
            }
        }
    }
}