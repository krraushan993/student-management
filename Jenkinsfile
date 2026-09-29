pipeline {

    agent any

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                bat 'mvn clean package'
            }
        }

        stage('Test') {
            steps {
                bat 'mvn test'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withCredentials([string(credentialsId: 'sonarqube-token', variable: 'SONAR_TOKEN')]) {
                    withSonarQubeEnv('SonarQube') {
                        bat 'mvn sonar:sonar -Dsonar.projectKey=Student-Management -Dsonar.token=%SONAR_TOKEN%'
                    }
                }
            }
        }

        stage('Docker Build') {
            steps {
                bat 'docker build -t student-management:1.0 .'
            }
        }

        stage('Docker Deploy') {
            steps {
                bat 'docker stop student-management-container || exit 0'
                bat 'docker rm student-management-container || exit 0'
                bat 'docker run -d -p 8081:8081 --name student-management-container student-management:1.0'
            }
        }
    }
}