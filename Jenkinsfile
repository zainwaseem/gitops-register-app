pipeline {
    agent { label "Jenkins-Agent" }
    environment {
              APP_NAME = "register-app-pipeline"
    }

    stages {
        stage("Cleanup Workspace") {
            steps {
                cleanWs()
            }
        }

        stage("Checkout from SCM") {
               steps {
                   git branch: 'main', credentialsId: 'github', url: 'https://github.com/zainwaseem/gitops-register-app'
               }
        }

        stage("Update the Deployment Tags") {
            steps {
                sh """
                   cat deployment.yaml
                   sed -i 's/${APP_NAME}.*/${APP_NAME}:${IMAGE_TAG}/g' deployment.yaml
                   cat deployment.yaml
                """
            }
        }

        // stage("Push the changed deployment file to Git") {
        //     steps {
        //         sh """
        //            git config --global user.name "zainwaseem"
        //            git config --global user.email "zainwaseem9371@gmail.com"
        //            git add deployment.yaml
        //            git commit -m "Updated Deployment Manifest"
        //         """
        //         withCredentials([gitUsernamePassword(credentialsId: 'github', gitToolName: 'Default')]) {
        //           sh "git push https://github.com/zainwaseem/gitops-register-app main"
        //         }
        //     }
        // }
        
        stage('Push Deployment Manifest') {
    steps {
        withCredentials([usernamePassword(credentialsId: 'github', usernameVariable: 'GIT_USER', passwordVariable: 'GIT_TOKEN')]) {
            sh '''
                git config --global user.name "${GIT_USER}"
                git config --global user.email "zainwaseem9371@gmail.com"
                
                # Set remote with credentials
                git remote set-url origin https://${GIT_USER}:${GIT_TOKEN}@github.com/zainwaseem/gitops-register-app.git
                
                git add deployment.yaml
                git commit -m "Updated Deployment Manifest" || echo "No changes to commit"
                git push origin main
            '''
        }
    }
}

      
    }
}
