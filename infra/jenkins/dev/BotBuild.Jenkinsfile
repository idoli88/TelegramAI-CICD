pipeline {
    agent {
        docker {
            // TODO build & push your Jenkins agent image, place the URL here
            image '700935310038.dkr.ecr.eu-west-2.amazonaws.com/idot-jenkins-agent'
            args  '--user root -v /var/run/docker.sock:/var/run/docker.sock'
        }
    }
    options {
        timestamps()

    }
    environment {

        REPO_NAME = '700935310038.dkr.ecr.eu-west-2.amazonaws.com'
        IMAGE_NAME = 'ci-cd-bot'
        IMAGE_TAG = '${BUILD_NUMBER}'
    }
    stages {
        stage('Build') {
            steps {
                // TODO dev bot build stage
                sh '''
                 aws ecr get-login-password --region eu-west-2 | docker login --username AWS --password-stdin 700935310038.dkr.ecr.eu-west-2.amazonaws.com
                 docker build -t ${IMAGE_NAME} -f ./bot/Dockerfile .
                 docker tag ${IMAGE_NAME} ${REPO_URL}/${IMAGE_NAME}:${BUILD_NUMBER}
                 docker push ${REPO_URL}/${IMAGE_NAME}:${BUILD_NUMBER}
                '''
            }
        }

        stage('Trigger Deploy') {

            steps {
                build job: 'BotDeploy', wait: false, parameters: [
                    string(name: 'BOT_IMAGE_NAME', value: "${REPO_URL}/${IMAGE_NAME}:${BUILD_NUMBER}")
                ]
            }
        }
    }
}