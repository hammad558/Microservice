pipeline {
    agent any
    
    environment {
        KUBE_SERVER     = 'https://5765636A4E6AE385683ED1AA12D36B5C.gr7.us-east-1.eks.amazonaws.com'
        KUBE_NAMESPACE  = 'webapps'
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', credentialsId: 'git-cred', url: 'https://github.com/hammad558/Microservice.git'
            }
        }

        stage('Deploy To Kubernetes') {
            steps {
                withCredentials([
                    string(credentialsId: 'k8-ca', variable: 'K8S_CA_TEXT'),
                    string(credentialsId: 'k8-token', variable: 'K8S_TOKEN')
                ]) {
                    sh '''
                        # Avoid echoing secrets to console
                        set +x

                        # Create CA file without masking issues
                        if echo "$K8S_CA_TEXT" | grep -q "BEGIN CERTIFICATE"; then
                            printf "%s" "$K8S_CA_TEXT" > ca.crt
                        else
                            echo "$K8S_CA_TEXT" | base64 -d > ca.crt
                        fi

                        # Restore debug
                        set -x

                        # Validate CA file
                        openssl x509 -in ca.crt -noout -text || { echo "Invalid CA cert"; exit 1; }

                        # Apply Kubernetes manifests
                        kubectl --token="$K8S_TOKEN" \
                                --server="$KUBE_SERVER" \
                                --certificate-authority=ca.crt \
                                --namespace="$KUBE_NAMESPACE" \
                                apply -f deployment-service.yml
                    '''
                }
            }
        }

        stage('Verify Deployment') {
            steps {
                sh '''
                    kubectl --token="$K8S_TOKEN" \
                            --server="$KUBE_SERVER" \
                            --certificate-authority=ca.crt \
                            --namespace="$KUBE_NAMESPACE" \
                            get pods -o wide
                '''
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
