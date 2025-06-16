pipeline {    
  agent any

    environment {
        OPENSHIFT_PROJECT = "jenkinsns" 
        IMAGE_NAME = "tictacimg" // can replace with any custom image name
        GIT_REPO = "https://github.com/snehamanoharan/tic-tac-toe-game.git" 
        GIT_REF = "main"  // if any other branch name you are using please mention that here
    }

    stages {
        stage('Deploy APP to OCP') {
            steps {
                script {
                  
                    // declare project name 
                    sh "oc project ${OPENSHIFT_PROJECT}"
                  
                    // Delete the existing app (DeploymentConfig, Service, ImageStream, etc.) if it exists
                    sh """
                        oc delete all --selector=app=${IMAGE_NAME} || true
                        oc delete imagestream ${IMAGE_NAME} || true
                        oc delete buildconfig ${IMAGE_NAME} || true
                    """
                  
                    // Deploy the application using OpenShift
                   // sh " oc new-app openshift/nginx~${GIT_REPO} --name=${IMAGE_NAME} --source-secret=${SECRET} --allow-missing-images "
                    sh " oc new-app openshift/nginx~${GIT_REPO} --name=${IMAGE_NAME} --allow-missing-images "
                  
                    //pass git creds to build config and start build again to apply 
                    sh "oc patch bc/${IMAGE_NAME} -p '{\"spec\":{\"source\":{\"git\":{\"uri\":\"${GIT_REPO}\",\"ref\":\"${GIT_REF}\"}}}}'"
                    sh "oc start-build ${IMAGE_NAME}"
                }
            }
        }

        stage('Expose Service') {
            steps {
                script {
                    // Expose the service to make it publicly available
                    sh "oc expose svc/${IMAGE_NAME}"
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully.'
        }
        failure {
            echo 'Pipeline failed.'
        }
    }
}
