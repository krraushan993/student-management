
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
                withCredentials([
                    string(
                        credentialsId: 'sonarqube-token',
                        variable: 'SONAR_TOKEN'
                    )
                ]) {
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
                bat '''
                    docker stop student-management-container 2>NUL
                    docker rm student-management-container 2>NUL
                    docker run -d --name student-management-container -p 8081:8081 student-management:1.0
                '''
            }
        }

        stage('Health Check') {
            steps {
                timeout(time: 2, unit: 'MINUTES') {
                    script {
                        int retries = 12
                        boolean healthy = false

                        for (int i = 0; i < retries; i++) {
                            int result = bat(
                                script: 'curl.exe -s -o NUL -w "%%{http_code}" http://localhost:8081/actuator/health',
                                returnStatus: true
                            )

                            if (result == 0) {
                                healthy = true
                                break
                            }

                            sleep(time: 5, unit: 'SECONDS')
                        }

                        if (!healthy) {
                            bat 'docker logs --tail 100 student-management-container'
                            error('Health Check failed!')
                        }

                        echo 'Health Check passed!'
                    }
                }
            }
        }

        stage('Logs') {
            steps {
                bat 'docker logs --tail 50 student-management-container'
            }
        }
    }
}