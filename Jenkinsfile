pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "my-app"
        STAGING_SERVER = "ubuntu@54.90.110.71"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Sagar170899/my-app.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    bat 'docker build -t %DOCKER_IMAGE% .'
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    bat 'npm test'
                }
            }
        }

        stage('Deploy to Staging') {
            steps {
                script {
                    // Push Docker image to EC2 instance
                    bat """
                    docker save %DOCKER_IMAGE% | bzip2 | plink -i E:\\PWSkills\\AWS\\ec2key.pem %STAGING_SERVER% "bunzip2 | docker load"
                    plink -i E:\\PWSkills\\AWS\\ec2key.pem %STAGING_SERVER% "docker stop my-app || true"
                    plink -i E:\\PWSkills\\AWS\\ec2key.pem %STAGING_SERVER% "docker run -d -p 80:3000 --name my-app %DOCKER_IMAGE%"
                    """
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment successful!'
        }
        failure {
            echo 'Deployment failed!'
        }
    }
}
