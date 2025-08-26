@Library('my-shared-library') _

pipeline {
    agent any
    parameters {
        string(name: 'BRANCH_NAME', defaultValue: 'main', description: 'Git Branch to build')
        string(name: 'IMAGE_TAG', defaultValue: 'latest', description: 'Docker Image Tag')
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: "${params.BRANCH_NAME}", url: 'https://github.com/alaaosama72/my-java-app.git'
            }
        }

        stage('Build & Test') {
            parallel {
                stage('Build JAR') {
                    steps {
                        buildJar()
                    }
                }
                stage('Run Tests') {
                    steps {
                        sh 'mvn test'
                    }
                }
            }
        }

        stage('Docker Build & Push') {
            steps {
                buildDocker(params.IMAGE_TAG)
                pushDocker(params.IMAGE_TAG)
            }
        }

        stage('Post Actions') {
            steps {
                cleanWs()
                sh 'docker logout || true'
                sh 'docker image prune -f || true'
            }
        }
    }
}
