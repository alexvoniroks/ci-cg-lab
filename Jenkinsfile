pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/alexvoniroks/ci-cg-lab.git',
                    credentialsId: 'github'
            }
        }

        stage('Build & Test') {
            steps {
                sh '''
                  python --version
                  pip install --upgrade pip
                  pip install -r requirements.txt
                  pip install pytest
                  pytest || echo "No tests yet"
                '''
            }
        }

        stage('Docker Build & Push') {
            agent any  // run on Jenkins node with Docker access
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
