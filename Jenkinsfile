pipeline {
    agent any

    stages {
        stage('Deploy To Kubernetes') {
            steps {
                withCredentials([
                    string(credentialsId: 'k8s-ca-text', variable: 'K8S_CA_TEXT'),
                    string(credentialsId: 'k8-token', variable: 'K8S_TOKEN')
                ]) {
                    sh '''
                        # Write CA text to file
                        echo "$K8S_CA_TEXT" > ca.crt

                        # Apply deployment
                        kubectl --token=$K8S_TOKEN \
                                --server=https://5765636A4E6AE385683ED1AA12D36B5C.gr7.us-east-1.eks.amazonaws.com \
                                --certificate-authority=ca.crt \
                                --namespace=webapps apply -f deployment-service.yml
                    '''
                }
            }
        }

        stage('Verify Deployment') {
            steps {
                withCredentials([
                    string(credentialsId: 'k8s-ca-text', variable: 'K8S_CA_TEXT'),
                    string(credentialsId: 'k8-token', variable: 'K8S_TOKEN')
                ]) {
                    sh '''
                        echo "$K8S_CA_TEXT" > ca.crt
                        kubectl --token=$K8S_TOKEN \
                                --server=https://5765636A4E6AE385683ED1AA12D36B5C.gr7.us-east-1.eks.amazonaws.com \
                                --certificate-authority=ca.crt \
                                --namespace=webapps get svc
                    '''
                }
            }
        }
    }
}
