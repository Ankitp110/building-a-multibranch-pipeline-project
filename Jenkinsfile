pipeline {
    agent {
        docker {
            image 'node:6-alpine'
            args '-p 3000:3000 -p 5000:5000'
        }

        parameters {
        string(name: 'BRANCH', description: 'Provide the branch name')
        string(name: 'REPOSITORY', 
        //defaultValue: 'https://github.com/Ankitp110/building-a-multibranch-pipeline-project', 
        description: 'Repo name provided')
        }
    }
    environment {
        CI = 'true'
    }

    stages {
        stage("Checkout") {
            steps{
                checkout([  
            $class: 'GitSCM', 
            branches: [[name: "*/${params.BRANCH}"]],  
            userRemoteConfigs: [[url: params.REPOSITORY]]
        ])
            }
        }

        stage('Build') {
            steps {
                sh 'npm install'
            }
        }
        stage('Test') {
            steps {
                sh './jenkins/scripts/test.sh'
            }
        }
        stage('Deliver for development') {
            when {
                branch 'development' 
            }
            steps {
                sh './jenkins/scripts/deliver-for-development.sh'
                input message: 'Finished using the web site? (Click "Proceed" to continue)'
                sh './jenkins/scripts/kill.sh'
            }
        }
        stage('Deploy for production') {
            when {
                branch 'production'  
            }
            steps {
                sh './jenkins/scripts/deploy-for-production.sh'
                input message: 'Finished using the web site? (Click "Proceed" to continue)'
                sh './jenkins/scripts/kill.sh'
            }
        }
    }
}