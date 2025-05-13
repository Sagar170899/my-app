pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "my-app"
        STAGING_SERVER = "ubuntu@54.90.110.71"
        PEM_FILE = "E:\\PWSkills\\AWS\\ec2key.pem"
        CONTAINER_NAME = "my-app"
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
                    bat """
                    REM Save Docker image to a tar file
                    docker save -o %DOCKER_IMAGE%.tar %DOCKER_IMAGE%
                    
                    REM Transfer the image to the remote server
                    pscp -i %PEM_FILE% %DOCKER_IMAGE%.tar %STAGING_SERVER%:/home/ubuntu/%DOCKER_IMAGE%.tar
                    
                    REM Load the image and run the container on the remote server
                    plink -i %PEM_FILE% %STAGING_SERVER% "docker load -i /home/ubuntu/%DOCKER_IMAGE%.tar"
                    plink -i %PEM_FILE% %STAGING_SERVER% "docker stop %CONTAINER_NAME% || true"
                    plink -i %PEM_FILE% %STAGING_SERVER% "docker rm %CONTAINER_NAME% || true"
                    plink -i %PEM_FILE% %STAGING_SERVER% "docker run -d -p 80:3000 --name %CONTAINER_NAME% %DOCKER_IMAGE%"
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
