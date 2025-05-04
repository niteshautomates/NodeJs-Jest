pipeline {
    agent {
        label 'linux'
    }
    tools {
       nodejs 'nodejs'
    }
    environment {
        SONAR_SCANNER_HOME = tool 'SonarQube'
    }
    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'develop', url: 'https://github.com/niteshautomates/NodeJs-Jest.git'
            }
        }
        stage('Dependencies'){
            steps{
                nodejs('nodejs') {
            sh 'npm install'
            }
            }
        }
        stage('Test'){
             steps{
                nodejs('nodejs') {
            sh 'npm run test'
            }
            }
        }
        stage('SonarQube Analysis') {
            steps {
                script {
                    withSonarQubeEnv('SonarQube') {
                        sh '''
                        ${SONAR_SCANNER_HOME}/bin/sonar-scanner -Dsonar.projectKey=NodeJs-Jest -Dsonar.sources=. -Dsonar.test.inclusions=**/*.test.js \
                        -Dsonar.javascript.lcov.reportPaths=reports/lcov.info
                        '''
                    }
                }
            }
        }
        stage("Quality Gate") {
            steps {
                script {
                    waitForQualityGate abortPipeline: false, credentialsId: 'sonar-token'
                }
            }
        }
        stage('Trivy File Scan') {
            steps {
                sh 'trivy fs --format table -o trivy_results.txt .'
            }
        }
    }
}

