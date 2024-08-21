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

        stage('Prepare Repository') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'GitHub-id', usernameVariable: 'GITHUB_USERNAME', passwordVariable: 'GITHUB_TOKEN')]) {
                        def repoDir = "gitops-app"
                        if (fileExists(repoDir)) {
                            dir(repoDir) {
                                sh "git checkout cd"

                                sh "git pull origin cd"
                            }
                        } else {
                            sh "git clone https://${GITHUB_USERNAME}:${GITHUB_TOKEN}@github.com/nuraybayrakdar/gitops-app.git"
                            dir(repoDir) {
                                sh "git checkout cd"
                            }
                        }
                    }
                }
            }
        }


        stage('Commit and Push Changes') {
            steps {
                script {
                     withCredentials([usernamePassword(credentialsId: 'GitHub-id', usernameVariable: 'GITHUB_USERNAME', passwordVariable: 'GITHUB_TOKEN')]) {
                        dir('gitops-app') {
                            // Git ayarlarını yap
                            sh "git config user.name 'nuraybayrakdar'"
                            sh "git config user.email 'bayrakdarnuray@gmail.com'"

                            // Değişiklikleri commit et ve pushla
                            sh "git add ."
                            sh "git commit -m 'Update deployment.yaml with new Docker image: ${params.dockerImage}' || echo 'No changes to commit'"
                            sh "git push https://${GITHUB_USERNAME}:${GITHUB_TOKEN}@github.com/nuraybayrakdar/gitops-app.git cd"
                        }
                    }
                }
            }
        }
    }
}
