pipeline {
    agent any

    environment {
        K8S_SERVER = "https://5765636A4E6AE385683ED1AA12D36B5C.gr7.us-east-1.eks.amazonaws.com"
        K8S_TOKEN  = credentials('k8-token') // Jenkins Secret Text with your SA token
        KUBE_NAMESPACE = "webapps"
        KUBECONFIG = "${env.WORKSPACE}/kubeconfig"
    }

    stages {

        stage('Checkout SCM') {
            steps {
                checkout scm
            }
        }

        stage('Configure kubectl') {
            steps {
                sh '''
                    # Create kubeconfig file in workspace
                    mkdir -p ${WORKSPACE}

                    kubectl config set-cluster my-cluster \
                        --server=${K8S_SERVER} \
                        --insecure-skip-tls-verify=true \
                        --kubeconfig=${KUBECONFIG}

                    kubectl config set-credentials jenkins-sa \
                        --token=${K8S_TOKEN} \
                        --kubeconfig=${KUBECONFIG}

                    kubectl config set-context my-context \
                        --cluster=my-cluster \
                        --namespace=${KUBE_NAMESPACE} \
                        --user=jenkins-sa \
                        --kubeconfig=${KUBECONFIG}

                    kubectl config use-context my-context --kubeconfig=${KUBECONFIG}

                    # Optional debug
                    kubectl config view --kubeconfig=${KUBECONFIG}
                    kubectl auth can-i create deployments --namespace=${KUBE_NAMESPACE} --kubeconfig=${KUBECONFIG}
                '''
            }
        }

        stage('Deploy To Kubernetes') {
            steps {
                sh '''
                    kubectl apply -f deployment-service.yml --validate=false --kubeconfig=${KUBECONFIG}
                '''
            }
        }

        stage('Verify Deployment') {
            steps {
                sh '''
                    kubectl get deployments -n ${KUBE_NAMESPACE} --kubeconfig=${KUBECONFIG}
                    kubectl get svc -n ${KUBE_NAMESPACE} --kubeconfig=${KUBECONFIG}
                '''
            }
        }
    }

    post {
        always {
            echo "Pipeline finished."
        }
        failure {
            echo "Pipeline failed. Check the logs."
        }
        success {
            echo "Deployment successful!"
        }
    }
}
