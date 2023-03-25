stage('BotDeploy') {
    steps {
        script {
            // Parse the image name from the previous stage's output
            def matcher = manager.getLogMatcher("Successfully built (.+)")
            if (matcher?.matches()) {
                env.BOT_IMAGE_NAME = matcher.group(1)
            } else {
                error("BOT_IMAGE_NAME not found in the previous stage's output")
            }
        }

        withCredentials([kubeconfig(credentialsId: 'kubeconfig', variable: 'KUBECONFIG')]) {
            sh """
            # Replace the image placeholder in the bot.yaml file
            sed "s|BOT_IMAGE_NAME|${env.BOT_IMAGE_NAME}|g" infra/k8s/bot.yaml > infra/k8s/bot_processed.yaml

            # Apply the configurations to the k8s cluster
            kubectl apply --kubeconfig \${KUBECONFIG} -f infra/k8s/bot_processed.yaml
            kubectl rollout status deployment/bot-deployment -n \${APP_ENV}
            kubectl get deployments bot-deployment -n \${APP_ENV}
            """
        }
    }
}
