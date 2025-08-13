pipeline {
    agent any

    environment {
        K8S_SERVER = 'https://5765636A4E6AE385683ED1AA12D36B5C.gr7.us-east-1.eks.amazonaws.com'// replace with your EKS endpoint
        K8S_NAMESPACE = 'webapps'
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/hammad558/Microservice.git',
                    credentialsId: 'git-cred'
            }
        }

        stage('Deploy To Kubernetes') {
            steps {
                withCredentials([
                    string(credentialsId: 'k8-token', variable: 'K8S_TOKEN'),
                    string(credentialsId: 'k8-ca', variable: 'K8S_CA_TEXT')
                ]) {
                    sh '''
                        # Restore newlines if Jenkins flattened them
                        echo "$K8S_CA_TEXT" | sed 's/\\\\n/\\n/g' > ca.crt
                        echo "CA cert written to file."

                        kubectl config set-cluster my-cluster \
                          --server=${K8S_SERVER} \
                          --certificate-authority=ca.crt \
                          --embed-certs=true

                        kubectl config set-credentials jenkins-sa --token=$K8S_TOKEN

                        kubectl config set-context my-context \
                          --cluster=my-cluster \
                          --namespace=${K8S_NAMESPACE} \
                          --user=jenkins-sa

                        kubectl config use-context my-context

                        # Apply deployment manifests
                        kubectl apply -f deployment-service.yml

                        # Verify pods
                        kubectl get pods -n ${K8S_NAMESPACE}
                    '''
                }
            }
        }
    }

    post {
        failure {
            echo 'Deployment failed.'
        }
        success {
            echo 'Deployment completed successfully.'
        }
    }
}
