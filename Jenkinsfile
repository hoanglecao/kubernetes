pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "react-app"
        ACR_NAME = "practicaldevops"
        ACR_LOGIN_SERVER = "practicaldevops.azurecr.io"
        ACR_CREDENTIALS_ID = "f9592263-0b9f-441b-b931-02108c3fa9e9"
        BRANCH_NAME = "${params.BRANCH_NAME}"
    }
     parameters {
        string(name: 'BRANCH_NAME', defaultValue: 'develop', description: 'Branch to build')
    }
    stages {
        stage('Print Branch Name') {
            steps {
                script {
                    echo "Building branch: ${BRANCH_NAME}"
                }
            }
        }
        stage('Checkout') {
            steps {        
                // Checkout code from the branch that triggered the build
                script {
                    git url: 'https://github.com/hoanglecao/kubernetes.git', branch: "${BRANCH_NAME}"
                }
            }
            
        }

        stage('Build Application') {
            steps {
                script {
                    sh 'npm install'
                    sh 'npm run build'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh """
                        docker build -t $DOCKER_IMAGE .
                        docker tag $DOCKER_IMAGE $ACR_LOGIN_SERVER/$DOCKER_IMAGE
                    """
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry("https://$ACR_LOGIN_SERVER", "$ACR_CREDENTIALS_ID") {
                        sh "docker push $ACR_LOGIN_SERVER/$DOCKER_IMAGE"
                    }
                }
            }
        }

        stage('Deploy to AKS') {
            steps {
                script {
                    sh """
                        kubectl apply -f frontent.yaml --namespace=devops                        
                    """
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline succeeded!'
        }
        failure {
            echo 'Pipeline failed!' 
        }
    }
}
