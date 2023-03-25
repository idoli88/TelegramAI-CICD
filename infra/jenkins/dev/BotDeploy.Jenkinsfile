pipeline {
    agent {
        docker {
            // TODO build & push your Jenkins agent image, place the URL here
            image '700935310038.dkr.ecr.eu-west-2.amazonaws.com/eli-abukrat-jenkins-agent:latest'
            args  '--user root -v /var/run/docker.sock:/var/run/docker.sock'
        }
    }

    environment {
        APP_ENV = "dev"
    }

    parameters {
        string(name: 'REGISTRY_URL')
        string(name: 'IMAGE_NAME')
        string(name: 'BUILD_NUMBER')
    }

    stages {
        stage('Bot Deploy') {
            withCredentials([kubeconfig(credentialsId: 'kubeconfig', variable: 'KUBECONFIG')]) {
                script {
                    // Parse the image name from the previous stage's output
                    def matcher = manager.getLogMatcher("Successfully built (.+)")
                    if (matcher?.matches()) {
                        env.BOT_IMAGE_NAME = matcher.group(1)
                    } else {
                        error("BOT_IMAGE_NAME not found in the previous stage's output")
                    }
                }

                sh """
                # Replace the image placeholder in the bot.yaml file
                sed "s|BOT_IMAGE_NAME|${BOT_IMAGE_NAME}|g" infra/k8s/bot.yaml > infra/k8s/bot_processed.yaml

                # Apply the configurations to the k8s cluster
                kubectl apply --kubeconfig ${KUBECONFIG} -f infra/k8s/bot_processed.yaml
                kubectl rollout status deployment/bot-deployment -n ${APP_ENV}
                kubectl get deployments bot-deployment -n ${APP_ENV}
                """
            }
        }
    }
}
