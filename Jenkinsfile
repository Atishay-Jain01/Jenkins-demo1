pipeline {
    agent any
    environment {
        AZURE_CREDENTIALS ID = credentials('azure-service-principal')
        RESOURCE_GROUP = 'rg-jenkins-demo1'
        APP_SERVICE_NAME = 'webapijenkinsdemo1'
    }
    
    stages {
        stage('Checkout') {
            steps {
                git branch: 'master', url: 'https://github.com/Atishay-Jain01/Jenkins-demo1.git'
            }
        }
        stage('Build') {
            steps {
                script {
                    bat 'dotnet restore'
                    bat 'dotnet build --configuration Release'
                    bat 'dotnet publish -c Release -o ./publish'
                }
            }
        }

        stage('Deploy to Azure') {
            steps {
                withCredentials([azureServicePrincipal('azure-service-principal')]) {
                    bat "az login --service-principal -u $AZURE_CLIENT_ID -p $AZURE_CLIENT_SECRET --tenant $AZURE_TENANT_ID"
                    bat "powershell Compress-Archive -Path ./publish/* -DestinationPath ./publish.zip -Force"
                    bat "az webapp deploy --resource-group $RESOURCE_GROUP --name $APP_SERVICE_NAME --src-path ./publish.zip --type zip"                
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment Successful!'
        }
        failure {
            echo 'Deployment Failed!'
        }
    }
}
