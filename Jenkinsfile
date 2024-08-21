pipeline {
    agent any

    parameters {
        string(name: 'dockerImage', defaultValue: '', description: 'Docker image to deploy')
    }

    environment {
        K8S_ID = 'K8S'
    }

    stages {
        stage('Validate Parameters') {
            steps {
                script {
                    if (!params.dockerImage) {
                        error "dockerImage parameter is missing."
                    }
                    echo "Deploying Docker image: ${params.dockerImage}"
                }
            }
        }

        stage('Update Deployment YAML') {
            steps {
                script {
                    sh "sed -i 's|<image_placeholder>|${params.dockerImage}|g' deployment.yaml"
                    sh "cat deployment.yaml"
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    withCredentials([file(credentialsId: K8S_ID, variable: 'KUBECONFIG')]) {
                        sh 'kubectl apply -f deployment.yaml'
                        sh 'cat deployment.yaml'
                    }
                }
            }
        }
    }
}
