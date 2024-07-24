pipeline {
    agent {
        label 'jenkins-agent'
    }
    environment {
        APP_NAME = "argocd-kubernetes-manifests"
    }
    stages {
        stage('Cleanup Workspace') {
            steps {
                cleanWs()
            }
        }
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

        stage('Push the changed deployment files to Git') {
            steps {
                sh '''
                    git config --global user.name "MaxNickerson"
                    git config --global user.email "bananamanbomber@gmail.com"
                    git add backend-deployment.yaml frontend-deployment.yaml
                    git commit -m "Updated Deployment Manifests"
                '''
                withCredentials([usernamePassword(credentialsId: 'github-pat', gitToolName: 'Default')]) {
                    sh 'git push https://github.com/MaxNickerson/argocd-kubernetes-manifests main'
                }
            }
        }
    }
}
