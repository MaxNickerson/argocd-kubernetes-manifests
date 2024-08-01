pipeline {
    agent {
        label 'jenkins-agent'
    }
    parameters {
        string(name: 'IMAGE_TAG', defaultValue: '', description: 'Latest Docker Image Tag')
    }
    environment {
        APP_NAME = "argocd-kubernetes-manifests"
    }
    stages {
        stage('Checkout from SCM') {
            steps {
                git branch: 'main', url: 'https://github.com/MaxNickerson/argocd-kubernetes-manifests'
            }
        }
        stage('Update the Deployment Tags') {
            steps {
                sh '''
                    echo "Updating frontend-deployment.yaml with IMAGE_TAG=${IMAGE_TAG}"
                    sed -i "s|{{IMAGE_TAG}}|${IMAGE_TAG}|g" frontend-deployment.yaml
                    echo "Updated frontend-deployment.yaml:"
                    cat frontend-deployment.yaml

                    echo "Updating backend-deployment.yaml with IMAGE_TAG=${IMAGE_TAG}"
                    sed -i "s|{{IMAGE_TAG}}|${IMAGE_TAG}|g" backend-deployment.yaml
                    echo "Updated backend-deployment.yaml:"
                    cat backend-deployment.yaml

                '''
            }
        }

        stage('Commit Changes') {
            steps {
                script {
                    sh 'git config --global user.name "MaxNickerson"'
                    sh 'git config --global user.email "bananamanbomber@gmail.com"'
                    sh '''
                        git add backend-deployment.yaml frontend-deployment.yaml
                        git commit -m "Updated Deployment Manifests" || echo "No changes to commit"
                    '''
                }
            }
        }

        stage('Push Changes') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'github-username-password', passwordVariable: 'GITHUB_TOKEN', usernameVariable: 'GITHUB_USER')]) {
                    sh 'git push https://${GITHUB_USER}:${GITHUB_TOKEN}@github.com/MaxNickerson/argocd-kubernetes-manifests.git main'
                }
            }
        }
        

    }
    post {
        always {
            cleanWs()
        }
    }
}
