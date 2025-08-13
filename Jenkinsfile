pipeline {
    agent any

    environment {
        K8S_SERVER = 'https://<your-cluster-endpoint>'  // from AWS EKS or kubeconfig
        NAMESPACE  = 'webapps'
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    credentialsId: 'git-cred',
                    url: 'https://github.com/hammad558/Microservice.git'
            }
        }

        stage('Deploy To Kubernetes') {
            steps {
                withCredentials([
                    string(credentialsId: 'k8-token', variable: 'K8S_TOKEN'),
                    string(credentialsId: 'k8-ca', variable: 'K8S_CA_TEXT')
                ]) {
                    sh '''
                        echo "$K8S_CA_TEXT" > ca.crt
                        echo "CA cert written to file."

                        kubectl config set-cluster my-cluster \
                          --server=$K8S_SERVER \
                          --certificate-authority=ca.crt \
                          --embed-certs=true

                        kubectl config set-credentials jenkins-sa \
                          --token=$K8S_TOKEN

                        kubectl config set-context my-context \
                          --cluster=my-cluster \
                          --namespace=$NAMESPACE \
                          --user=jenkins-sa

                        kubectl config use-context my-context

                        kubectl get pods
                    '''
                }
            }
        }
    }

    post {
        failure {
            echo 'Deployment failed.'
        }
    }
}
