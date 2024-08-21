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
                    echo "${params.dockerImage}"
                    sh "cat deployment.yaml"
                }
            }
        }

        stage('Commit and Push Changes') {
            steps {
                script {
                    withCredentials([string(credentialsId: 'GitHub-id')]) {
                        // Configure Git user without email
                        sh "git config user.name 'nuraybayrakdar'"
                        
                        // Add, commit and push changes
                        sh "git add deployment.yaml"
                        sh "git commit -m 'Update deployment.yaml with new Docker image: ${params.dockerImage}'"
                        sh "git push https://github.com/nuraybayrakdar/gitops-app.git main"  // Adjust URL and branch as needed
                    }
                }
            }
        }
    }
}
