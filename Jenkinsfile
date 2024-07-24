pipeline {
    agent {
        label 'jenkins-agent'
    }
    environment {
        APP_NAME = "argocd-kubernetes-manifests"
    }
    stages {
        stage('Checkout from SCM') {
            steps {
                git branch: 'main', credentialsId: 'github-pat', url: 'https://github.com/MaxNickerson/argocd-kubernetes-manifests'
            }
        }
        stage('Update the Deployment Tags') {
            steps {
                sh '''
                    # frontend
                    cat frontend-deployment.yaml
                    sed -i "s/APP_NAME/${APP_NAME}/g; s/IMAGE_TAG/${IMAGE_TAG}/g" frontend-deployment.yaml
                    cat frontend-deployment.yaml

                    # backend
                    cat backend-deployment.yaml
                    sed -i "s/APP_NAME/${APP_NAME}/g; s/IMAGE_TAG/${IMAGE_TAG}/g" backend-deployment.yaml
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
                withCredentials([usernamePassword(credentialsId: 'github-pat', passwordVariable: 'GITHUB_TOKEN', usernameVariable: 'GITHUB_USER')]) {
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
