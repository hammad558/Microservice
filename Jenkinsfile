pipeline {
    agent any

    environment {
        K8S_TOKEN = credentials('k8-token')  // Secret Text in Jenkins
        K8S_SERVER = 'https://5765636A4E6AE385683ED1AA12D36B5C.gr7.us-east-1.eks.amazonaws.com'
        K8S_NAMESPACE = 'webapps'
    }

    stages {
        stage('Deploy To Kubernetes') {
            steps {
                sh """
                    kubectl --token="${K8S_TOKEN}" \
                            --server=${K8S_SERVER} \
                            --insecure-skip-tls-verify=true \
                            apply -f deployment-service.yml -n ${K8S_NAMESPACE}
                """
            }
        }

        stage('Verify Deployment') {
            steps {
                sh """
                    kubectl --token="${K8S_TOKEN}" \
                            --server=${K8S_SERVER} \
                            --insecure-skip-tls-verify=true \
                            get svc -n ${K8S_NAMESPACE}
                """
            }
        }
    }
}
