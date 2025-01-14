pipeline {
    agent any

    stages {
        stage('Docker Version') {
            steps {
                bat 'docker --version'
            }
        }
        stage('Build Docker Image') {
            steps {
                bat 'docker build -t uadb-dev:1.0 .'
            }
        }
       
         stage('Tag Docker Images') {
            steps {
                bat 'docker tag uadb-dev:1.0 bissimilahi/uadb-dev:1.0'
            }
        }
         stage('Lister les images docker') {
            steps {
                bat 'docker images'
            }
        }
    }
}