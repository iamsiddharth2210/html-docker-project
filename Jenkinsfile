
pipeline {
    agent any

    options {
        skipDefaultCheckout(true)
    }

    triggers {
        pollSCM('H/2 * * * *')
    }

    environment {
        IMAGE_NAME = 'html-website'
        CONTAINER_NAME = 'html-app'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh 'docker build -t html-website:${BUILD_NUMBER} .'
                sh 'docker tag html-website:${BUILD_NUMBER} html-website:latest'
            }
        }

        stage('Test') {
            steps {
                sh 'test -s index.html'
                sh 'docker image inspect html-website:latest'
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                    docker rm -f html-app || true
                    docker run -d \
                      --name html-app \
                      --restart unless-stopped \
                      -p 8080:80 \
                      html-website:latest
                '''
            }
        }
    }

    post {
        success {
            echo 'Deployment completed successfully!'
        }
        failure {
            echo 'Pipeline failed. Review the console output.'
        }
    }
}
