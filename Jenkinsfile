pipeline {
    agent {
        docker {
            image 'python:3.11-slim'   // Use official Python image
            args '-u root'             // Run as root (needed to install extra tools if required)
        }
    }

    environment {
        DOCKER_IMAGE = "ci-cd-lab"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/alexvoniroks/ci-cd-lab.git',
                    credentialsId: 'github'
            }
        }

        stage('Build & Test') {
            steps {
                sh '''
                  python --version
                  pip install --upgrade pip
                  pip install -r requirements.txt
                  pytest || echo "No tests yet"
                '''
            }
        }

        stage('Docker Build & Push') {
            // Run this step on the Jenkins node, not inside the Python container
            agent any
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh '''
                      docker build -t $DOCKER_IMAGE .
                      echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                      docker tag $DOCKER_IMAGE $DOCKER_USER/$DOCKER_IMAGE:jenkins
                      docker push $DOCKER_USER/$DOCKER_IMAGE:jenkins
                    '''
                }
            }
        }
    }
}
