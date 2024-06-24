pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "react-app"
        ACR_NAME = "practicaldevops"
        ACR_LOGIN_SERVER = "practicaldevops.azurecr.io"
        ACR_CREDENTIALS_ID = "f9592263-0b9f-441b-b931-02108c3fa9e9"
        WORKDIR = 'src/frontend'

    }
   
    stages {
        stage('Checkout') {
            steps {        
                // Checkout code from the branch that triggered the build
                script {
                    git url: 'https://github.com/hoanglecao/kubernetes.git', branch: "develop"
                }
            }
            
        }

        stage('Build Docker Image') {
            steps {
                script {
                    dir("${WORKDIR}") {
                        sh """
                        docker build -t $DOCKER_IMAGE .
                        docker tag $DOCKER_IMAGE $ACR_LOGIN_SERVER/$DOCKER_IMAGE
                        """

                    }
                    
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    dir("${WORKDIR}") {
                        docker.withRegistry("https://$ACR_LOGIN_SERVER", "$ACR_CREDENTIALS_ID") {
                        sh "docker push $ACR_LOGIN_SERVER/$DOCKER_IMAGE"
                    }
                }
                    }
                    
            }
        }

        stage('Deploy to AKS') {
            steps {
                script {
                     dir("${WORKDIR}") {
                    sh """
                        kubectl apply -f frontent.yaml --namespace=devops                        
                    """
                     }
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
