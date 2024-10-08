pipeline {
    agent any

    stages {
        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("hossam7/nhorizon-java-app:latest")
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'docker-hub-credentials') {
                        docker.image("hossam7/nhorizon-java-app:latest").push()
                    }
                }
            }
        }

        stage('Pull Docker Image on Another Agent') {
            agent {
                label 'test2-agent'
            }
            steps {
                script {
                    docker.image("hossam7/nhorizon-java-app:latest").pull()
                }
            }
        }

        stage('Run Docker Image') {
            agent {
                label 'test2-agent'
            }
            steps {
                script {
                    docker.image("hossam7/nhorizon-java-app:latest").run('-d -p 8080:8080')
                }
            }
        }

        stage('Check Connectivity') {
            agent {
                label 'test2-agent'
            }
            steps {
                script {
                    def response = sh(script: "curl -o /dev/null -s -w \"%{http_code}\" http://localhost:8080", returnStdout: true).trim()
                    if (response == "200") {
                        echo "Website is accessible."
                    } else {
                        error("Website is not accessible, pipeline failed.")
                    }
                }
            }
        }
    }
}

