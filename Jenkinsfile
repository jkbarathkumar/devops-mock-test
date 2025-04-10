pipeline {
    agent any
    environment {
        REGISTRY = "barathkumar29/microservice-application"
        IMAGE_TAG = "${env.BRANCH_NAME}-${env.BUILD_NUMBER}"
        SONARQUBE_ENV = "SonarQube"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build & Test') {
            steps {
                sh 'mvn clean install'
            }
        }

        stage('SonarQube Analysis') {
            when {
                anyOf {
                    branch pattern: "feature/.*", comparator: "REGEXP"
                    branch 'develop'
                }
            }
            steps {
                withSonarQubeEnv("${SONARQUBE_ENV}") {
                    sh 'mvn sonar:sonar'
                }
            }
        }

        stage('Quality Gate') {
            when {
                anyOf {
                    branch pattern: "feature/.*", comparator: "REGEXP"
                    branch 'develop'
                }
            }
            steps {
                timeout(time: 1, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
    }
}
