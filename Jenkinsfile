pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "my-app"
        STAGING_SERVER = "ec2-user@<AWS-EC2-IP>"
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/Sagar170899/my-app.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE .'
            }
        }

        stage('Test') {
            steps {
                sh 'npm test'
            }
        }

        stage('Deploy to Staging') {
            steps {
                script {
                    // Push Docker image to EC2 instance
                    sh '''
                    docker save $DOCKER_IMAGE | bzip2 | ssh $STAGING_SERVER "bunzip2 | docker load"
                    ssh $STAGING_SERVER "docker stop my-app || true"
                    ssh $STAGING_SERVER "docker run -d -p 80:3000 --name my-app $DOCKER_IMAGE"
                    '''
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
