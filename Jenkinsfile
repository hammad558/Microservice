stage('Deploy To Kubernetes') {
    steps {
        withCredentials([
            string(credentialsId: 'k8s-token', variable: 'K8S_TOKEN'),
            string(credentialsId: 'k8s-ca', variable: 'K8S_CA_TEXT')
        ]) {
            sh '''
                # Write PEM CA cert directly
                echo "$K8S_CA_TEXT" > ca.crt
                echo "CA cert written to file."

                kubectl config set-cluster my-cluster \
                  --server=https://<your-cluster-endpoint> \
                  --certificate-authority=ca.crt \
                  --embed-certs=true

                kubectl config set-credentials jenkins-sa --token=$K8S_TOKEN

                kubectl config set-context my-context \
                  --cluster=my-cluster \
                  --namespace=webapps \
                  --user=jenkins-sa

                kubectl config use-context my-context

                kubectl get pods
            '''
        }
    }
}
