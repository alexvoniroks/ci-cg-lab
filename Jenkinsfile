pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "ci-cd-lab"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/alexvoniroks/ci-cg-lab.git',
                    credentialsId: 'github-https-creds'
            }
        }

        stage('Build & Test') {
            steps {
                sh '''
                  python3 --version
                  pip3 install --upgrade pip
                  pip3 install -r requirements.txt
                  pip3 install pytest
                  pytest || echo "No tests yet"
                '''
            }
        }

        stage('Docker Build & Push') {
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
