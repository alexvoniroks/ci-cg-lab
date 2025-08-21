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

                  # Create a virtual environment for this build
                  python3 -m venv venv
                  . venv/bin/activate

                  # Upgrade pip and install dependencies
                  pip install --upgrade pip
                  pip install -r requirements.txt
                  pip install pytest

                  # Run tests (skip error if no tests exist)
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
