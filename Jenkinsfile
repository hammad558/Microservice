pipeline {
    agent any

    environment {
        K8S_SERVER = "https://5765636A4E6AE385683ED1AA12D36B5C.gr7.us-east-1.eks.amazonaws.com"
        K8S_TOKEN  = credentials('k8-token') // Jenkins Secret Text with your SA token
    }

    stages {
        stage('Configure kubectl') {
            steps {
                sh '''
                    mkdir -p $HOME/.kube

                    kubectl config set-cluster my-cluster \
                        --server=${K8S_SERVER} \
                        --insecure-skip-tls-verify=true

                    kubectl config set-credentials jenkins-sa \
                        --token=${K8S_TOKEN}

                    kubectl config set-context my-context \
                        --cluster=my-cluster \
                        --namespace=webapps \
                        --user=jenkins-sa

                    kubectl config use-context my-context
                '''
            }
        }

        stage('Deploy To Kubernetes') {
            steps {
                sh 'kubectl apply -f deployment-service.yml'
            }
        }

        stage('Verify Deployment') {
            steps {
                sh 'kubectl get svc -n webapps'
            }
        }
    }
}
