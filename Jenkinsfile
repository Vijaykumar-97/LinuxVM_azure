pipeline {
    agent any

    parameters {
        choice(name: 'TERRAFORM_ACTION', choices: ['plan', 'apply', 'destroy'], description: 'Select Terraform action')
        string(name: 'AZURE_SUBSCRIPTION_ID', description: 'Azure Subscription ID')
    }

    stages {
        stage('Checkout Code') {
            steps {
                checkout([$class: 'GitSCM',
                          branches: [[name: '*/main']],
                          userRemoteConfigs: [[url: 'https://github.com/Vijaykumar-97/LinuxVM_azure.git']],
                          credentialsId: 'Github_secret'])
            }
        }

        stage('Terraform Init') {
            steps {
                script {
                    def azureSubscriptionId = env.AZURE_SUBSCRIPTION_ID
                    withCredentials([string(credentialsId: 'azure-client-id', variable: 'AZURE_CLIENT_ID'),
                                     string(credentialsId: 'secret_value', variable: 'AZURE_CLIENT_SECRET'),
                                     string(credentialsId: 'azure-tenant-id', variable: 'AZURE_TENANT_ID')]) {
                        sh """
                            /opt/homebrew/bin/az login --service-principal --username \$AZURE_CLIENT_ID --password \$AZURE_CLIENT_SECRET --tenant \$AZURE_TENANT_ID
                            /opt/homebrew/bin/terraform init
                            /opt/homebrew/bin/az logout
                        """
                    }
                }
            }
        }

        stage('Terraform Command') {
            steps {
                script {
                    def terraformAction = params.TERRAFORM_ACTION
                    def terraformCmd = "terraform ${terraformAction}"
                    def azureSubscriptionId = env.AZURE_SUBSCRIPTION_ID
                    withCredentials([string(credentialsId: 'azure-client-id', variable: 'AZURE_CLIENT_ID'),
                                     string(credentialsId: 'secret_value', variable: 'AZURE_CLIENT_SECRET'),
                                     string(credentialsId: 'azure-tenant-id', variable: 'AZURE_TENANT_ID')]) {
                        sh """
                            /opt/homebrew/bin/az login --service-principal --username \$AZURE_CLIENT_ID --password \$AZURE_CLIENT_SECRET --tenant \$AZURE_TENANT_ID
                            /opt/homebrew/bin/terraform ${terraformAction}
                            /opt/homebrew/bin/az logout
                        """
                    }
                }
            }
        }
    }
}
