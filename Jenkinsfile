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

        stage('Docker Push') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-credentials',
                    usernameVariable: 'DOCKER_USERNAME',
                    passwordVariable: 'DOCKER_PASSWORD'
                )]) {

                    bat 'docker login -u %DOCKER_USERNAME% -p %DOCKER_PASSWORD%'

                    bat 'docker tag student-management:1.0 %DOCKER_USERNAME%/student-management:1.0'

                    bat 'docker push %DOCKER_USERNAME%/student-management:1.0'
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                bat 'kubectl apply -f deployment.yaml'
                bat 'kubectl apply -f service.yaml'
                bat 'kubectl rollout status deployment/student-management'
            }
        }
    }
}