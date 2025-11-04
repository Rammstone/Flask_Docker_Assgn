pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub')
        IMAGE_NAME = "samridhr/flask-jenkins-demo"
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/Rammstone/Flask_Docker_Assgn.git',
                    credentialsId: 'github-token'
            }
        }


        stage('Build Docker Image') {
            steps {
                bat 'docker build -t $IMAGE_NAME .'
            }
        }

        stage('Run Container for Testing') {
            steps {
                bat 'docker run -d -p 5000:5000 --name test-container $IMAGE_NAME'
                bat 'sleep 5'
                bat 'curl -f http://localhost:5000 || (echo "Test failed!" && exit 1)'
            }
            post {
                always {
                    bat 'docker stop test-container || true'
                    bat 'docker rm test-container || true'
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    bat 'echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin'
                    bat 'docker push $IMAGE_NAME'
                }
            }
        }
    }
}
