pipeline {
    agent any
    environment {
        DOCKER_CONTEXT = 'desktop'
        PATH = "/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin"
        DOCKER_CREDENTIALS_ID = 'dockerhub-credentials'
        GIT_CREDENTIALS_ID = 'github-credentials'
        REGISTRY = 'nmbodj'
        FRONTEND_IMAGE = 'frontend'
        BACKEND_IMAGE = 'backend'
    }

    stages {
        stage('Set Docker Context') {
            steps {
                script {
                    echo 'docker context use ${DOCKER_CONTEXT}'
                }
            }
        }

        stage('Clone Repository') {
            steps {
                script {
                    checkout([$class: 'GitSCM',
                        branches: [[name: 'main']],
                        userRemoteConfigs: [[
                            url: 'git@github.com:CarrickDev/ml_project.git',
                            credentialsId: GIT_CREDENTIALS_ID
                        ]]
                    ])
                }
            }
        }

        stage('Test Docker') {
            steps {
                script {
                    echo 'docker --version'
                }
            }
        }

        stage('Build Backend Docker Image') {
            steps {
                script {
                    echo 'docker build -t ${REGISTRY}/${BACKEND_IMAGE}:latest ./backend'
                }
            }
        }

        stage('Build Frontend Docker Image') {
            steps {
                script {
                    echo 'docker build -t ${REGISTRY}/${FRONTEND_IMAGE}:latest ./frontend'
                }
            }
        }

       stage('Push Docker Images to DockerHub') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: DOCKER_CREDENTIALS_ID, usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASSWORD')]) {
                        echo 'docker context use desktop-linux'
                        echo 'echo $DOCKER_PASSWORD | docker login -u $DOCKER_USER --password-stdin'
                        echo 'docker push ${REGISTRY}/${BACKEND_IMAGE}:latest'
                        echo 'docker push ${REGISTRY}/${FRONTEND_IMAGE}:latest'
                    }
                }
            }
}

        stage('Apply Kubernetes Configurations') {
            steps {
                script {
                    try {
                        echo 'kubectl apply -f database/mysql-configmap.yml'
                        echo 'kubectl apply -f backend/manifest/backend-configmap.yml'
                        echo 'kubectl apply -f frontend/manifest/frontend-configmap.yml'
                        echo 'kubectl apply -f database/mysql-secret.yml'
                        echo 'kubectl apply -f database/mysql-pvc.yml'
                    } catch (Exception e) {
                        currentBuild.result = 'FAILURE'
                        throw e
                    }
                }
            }
        }

        stage('Deploy Applications to Kubernetes') {
            steps {
                script {
                    echo 'kubectl apply -f namespace.yml'
                    echo 'kubectl apply -f database/mysql-deployment.yml'
                    echo 'kubectl apply -f backend/manifest/backend-deployment.yml'
                    echo 'kubectl apply -f frontend/manifest/frontend-deployment.yml'
                    echo 'kubectl apply -f database/mysql-service.yml'
                    echo 'kubectl apply -f backend/manifest/backend-service.yml'
                    echo 'kubectl apply -f frontend/manifest/frontend-service.yml'
                }
            }
        }
    }

    post {
        success {
            echo 'Le pipeline CI/CD a été exécuté avec succès !'
        }
        failure {
            echo 'Le pipeline CI/CD a échoué.'
        }
    }
}
