pipeline {
    agent any

    environment {
        KUBE_SERVER = 'https://5765636A4E6AE385683ED1AA12D36B5C.gr7.us-east-1.eks.amazonaws.com'
        KUBE_NAMESPACE = 'webapps'
    }

    stages {

        stage('Checkout Code') {
            steps {
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: '*/main']],
                    doGenerateSubmoduleConfigurations: false,
                    extensions: [],
                    userRemoteConfigs: [[
                        url: 'https://github.com/hammad558/Microservice.git',
                        credentialsId: 'git-cred'
                    ]]
                ])
            }
        }

        stage('Deploy To Kubernetes') {
            steps {
                withCredentials([
                    string(credentialsId: 'k8-ca', variable: 'K8S_CA_TEXT'),
                    string(credentialsId: 'k8-token', variable: 'K8S_TOKEN')
                ]) {
                    sh '''
                        echo "$K8S_CA_TEXT" > ca.crt
                        kubectl --token=$K8S_TOKEN \
                                --server=$KUBE_SERVER \
                                --certificate-authority=ca.crt \
                                --namespace=$KUBE_NAMESPACE apply -f deployment-service.yml
                    '''
                }
            }
        }

        stage('Verify Deployment') {
            steps {
                withCredentials([
                    string(credentialsId: 'k8-ca', variable: 'K8S_CA_TEXT'),
                    string(credentialsId: 'k8-token', variable: 'K8S_TOKEN')
                ]) {
                    sh '''
                        echo "$K8S_CA_TEXT" > ca.crt
                        kubectl --token=$K8S_TOKEN \
                                --server=$KUBE_SERVER \
                                --certificate-authority=ca.crt \
                                --namespace=$KUBE_NAMESPACE get pods
                    '''
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment succeeded!'
        }
        failure {
            echo 'Deployment failed.'
        }
    }
}
