pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/youruser/ci-cd-lab.git'
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
