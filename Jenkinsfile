pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "react-app"
        ACR_NAME = "practicaldevops"
        ACR_LOGIN_SERVER = "practicaldevops.azurecr.io"
        ACR_CREDENTIALS_ID = "azure-sp-credentials" 
        WORKDIR = 'src/frontend'
        TENANT_ID = "ccbbcd23-7475-465d-923e-d6fdd342f209"

    }
   
    stages {
       
        stage('Login to Azure') {
            steps {
               echo "AZURE_CREDENTIALS_ID: ${env.ACR_CREDENTIALS_ID}"
                withCredentials([usernamePassword(credentialsId: "${env.ACR_CREDENTIALS_ID}", usernameVariable: 'AZURE_CLIENT_ID', passwordVariable: 'AZURE_CLIENT_SECRET')]) {
                    sh """
                        echo "AZURE_CLIENT_ID" : $AZURE_CLIENT_ID
                        az login --service-principal -u $AZURE_CLIENT_ID -p $AZURE_CLIENT_SECRET --tenant $TENANT_ID
                        az acr login --name $ACR_NAME
                    """
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    dir("${WORKDIR}") {
                        sh """
                        docker build -t $DOCKER_IMAGE $ACR_LOGIN_SERVER/$DOCKER_IMAGE
                        """

                    }
                    
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: "${env.ACR_CREDENTIALS_ID}", passwordVariable: 'PASSWORD', usernameVariable: 'USERNAME')]) {
                    sh """
                        echo $PASSWORD | docker login $ACR_LOGIN_SERVER -u $USERNAME --password-stdin
                        docker push $ACR_LOGIN_SERVER/$DOCKER_IMAGE
                    """
                }
                }
            }
        }

        stage('Deploy to AKS') {
            steps {
                script {
                    echo 'Start deploying image to AKS!'
                     dir("${WORKDIR}") {
                    sh """
                        kubectl apply -f frontent.yaml --namespace=devops                        
                    """
                     }
                     echo 'Deploy image to AKS succeeded!'
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
