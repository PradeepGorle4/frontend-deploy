pipeline {
    agent { label 'AGENT-1'}
    environment {
        PROJECT = 'expense'
        COMPONENT = 'frontend'
        DEPLOY_TO = 'QA'
        REGION = 'us-east-1'
        appVersion = ''
        environment = ''
    }
    options {
        disableConcurrentBuilds ()
        timeout(time: 30, unit: 'MINUTES')
    }
    parameters {
        string(name: 'version', description: 'Please Enter the application version')
        choice(name: 'deploy_to', choices: ['dev', 'qa', 'prod'], description: 'pick environment')
    }

    stages {
        stage('Setup Environment') {
            steps {
                script{
                    appVersion = params.version
                    environment = params.deploy_to
                }
            }
        }
        stage('Deploy') {        
            steps {
                script{
                    withAWS(region: 'us-east-1', credentials: 'aws-creds') {
                        sh """
                            aws eks update-kubeconfig --region $REGION --name expense-dev
                            kubectl get nodes
                            cd helm
                            sed -i 's/IMAGE_VERSION/${params.version}/g' values-${environment}.yaml
                            helm upgrade --install $COMPONENT -n $PROJECT -f values-${environment}.yaml .
                        """
                    }
                }
            }
        }
        
    }
    post {
        always {
            echo "I will always say hello-again"
            deleteDir()
        }
        failure {
            echo "I will run when pipeline fails"
        }
        success {
            echo "I will run when pipeline succeeds"
        }
    }
}