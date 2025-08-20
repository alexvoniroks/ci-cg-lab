pipeline {
    agent {
        docker {
            image 'python:3.11-slim'
            args '-u root'  // allows installing extra packages if needed
        }
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/alexvoniroks/ci-cd-lab.git',
                    credentialsId: 'github-https-creds'
            }
        }
        stage('Build & Test') {
            steps {
                sh 'pip install -r requirements.txt'
                sh 'pytest || echo "No tests yet"'
            }
        }
        stage('Docker Build & Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh '''
                      docker build -t ci-cd-lab .
                      echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                      docker tag ci-cd-lab $DOCKER_USER/ci-cd-lab:jenkins
                      docker push $DOCKER_USER/ci-cd-lab:jenkins
                    '''
                }
            }
        }
    }
}
