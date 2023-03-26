pipeline {
    ...
    stages {
        stage('Bot Deploy') {
            steps {
                script {
                    env.BOT_IMAGE_NAME = env.BOT_IMAGE_NAME.toLowerCase()
                }
                echo "${BOT_IMAGE_NAME}"
                withCredentials([
                    file(credentialsId: 'kubeconfig', variable: 'KUBECONFIG')
                ]) {
                    sh '''
                    # Replace the image placeholder in the bot.yaml file
                    sed "s|PLACEHOLDER_BOT_IMAGE_NAME|${env.BOT_IMAGE_NAME}|g" infra/k8s/bot.yaml > infra/k8s/bot_processed.yaml

                    # Apply the configurations to k8s cluster
                    kubectl set image deployment/bot-deployment bot=${env.BOT_IMAGE_NAME} -n ${APP_ENV}

                    kubectl apply --kubeconfig ${KUBECONFIG} -f infra/k8s/bot_processed.yaml --namespace dev
                    '''
                }
            }
        }
    }
}
