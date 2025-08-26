pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'alaa178/my-java-app'
    }

    parameters {
        string(name: 'IMAGE_TAG', defaultValue: "build-${env.BUILD_NUMBER}", description: 'Docker image tag')
        booleanParam(name: 'PUSH_IMAGE', defaultValue: true, description: 'Push image to Docker Hub?')
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build JAR') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE:${params.IMAGE_TAG} .'
            }
        }

        stage('Docker Login & Push') {
            when { expression { return params.PUSH_IMAGE } }
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds',
                                                  usernameVariable: 'DOCKER_USERNAME',
                                                  passwordVariable: 'DOCKER_PASSWORD')]) {
                    sh 'echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USERNAME" --password-stdin'
                    sh 'docker push $DOCKER_IMAGE:${params.IMAGE_TAG}'
                }
            }
        }
    }

    post {
        always {
            cleanWs()
            sh 'docker logout || true'
            sh 'docker image prune -f || true'
        }
    }
}
