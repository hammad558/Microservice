pipeline {
    agent any

    environment {
        // Jenkins credentials: one Secret Text, one Secret File
        K8S_TOKEN  = credentials('k8-token')   // secret text from `kubectl get secret ... | base64 --decode`
        K8S_CA     = credentials('k8-ca')      // secret file from `ca.crt` contents
        K8S_SERVER = 'https://5765636A4E6AE385683ED1AA12D36B5C.gr7.us-east-1.eks.amazonaws.com'
        K8S_NS     = 'webapps'
    }

    stages {
        stage('Deploy To Kubernetes') {
            steps {
                script {
                    // Save CA cert file
                    writeFile file: 'ca.crt', text: K8S_CA

                    sh """
                        kubectl --token="${K8S_TOKEN}" \
                                --server=${K8S_SERVER} \
                                --certificate-authority=ca.crt \
                                --namespace=${K8S_NS} \
                                apply -f deployment-service.yml
                    """
                }
            }
        }

        stage('Verify Deployment') {
            steps {
                script {
                    sh """
                        kubectl --token="${K8S_TOKEN}" \
                                --server=${K8S_SERVER} \
                                --certificate-authority=ca.crt \
                                --namespace=${K8S_NS} \
                                get svc
                    """
                }
            }
        }
    }
}
