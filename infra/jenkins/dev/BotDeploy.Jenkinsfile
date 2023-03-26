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
        string(name: 'BOT_IMAGE_NAME')
    }

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
                    sed "s|BOT_IMAGE_NAME|${BOT_IMAGE_NAME}|g" infra/k8s/bot.yaml > infra/k8s/bot_processed.yaml

                    # Apply the configurations to k8s cluster
                    kubectl set image deployment/bot-deployment bot=${BOT_IMAGE_NAME} -n ${APP_ENV}

                    kubectl apply --kubeconfig ${KUBECONFIG} -f infra/k8s/bot_processed.yaml --namespace dev

                    '''
                }
            }
        }
    }
}
