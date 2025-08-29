pipeline {
    agent { label "Jenkins-Agent" }

    environment {
        APP_NAME   = "zaid57/swiggy-clone"
        GITOPS_REPO = "https://github.com/zainwaseem/gitops-register-app.git"
    }

    stages {
        stage("Cleanup Workspace") {
            steps {
                cleanWs()
            }
        }

        stage("Checkout from GitOps Repo") {
            steps {
                git branch: 'main', credentialsId: 'github', url: "${GITOPS_REPO}"
            }
        }

        stage("Update the Deployment Image Tag") {
            steps {
                sh '''
                   echo "Before update:"
                   grep "image:" deployment.yaml || true

                   sed -i "s|image: .*|image: ${APP_NAME}:${IMAGE_TAG}|g" deployment.yaml

                   echo "After update:"
                   grep "image:" deployment.yaml || true
                '''
            }
        }

        stage("Push the Updated Manifest to GitOps Repo") {
            steps {
                withCredentials([usernamePassword(credentialsId: 'github', usernameVariable: 'GIT_USER', passwordVariable: 'GIT_TOKEN')]) {
                    sh '''
                        git config --global user.name "${GIT_USER}"
                        git config --global user.email "zainwaseem9371@gmail.com"

                        git remote set-url origin https://${GIT_USER}:${GIT_TOKEN}@github.com/zainwaseem/gitops-register-app.git

                        git add deployment.yaml
                        git commit -m "Updated deployment to image ${APP_NAME}:${IMAGE_TAG}" || echo "No changes to commit"
                        git push origin main
                    '''
                }
            }
        }
    }
}
