pipeline {
    agent {
        docker {
            // TODO build & push your Jenkins agent image, place the URL here
            image '700935310038.dkr.ecr.eu-west-2.amazonaws.com/eli-abukrat-jenkins-agent:latest'
            args  '--user root -v /var/run/docker.sock:/var/run/docker.sock'
        }
    }

    options {
        timestamps()
    }

    environment {
        REGISTRY_URL = '700935310038.dkr.ecr.eu-west-2.amazonaws.com'
        IMAGE_NAME = 'idot_worker_prod'
        IMAGE_TAG = '${GIT_COMMIT}'

    }




    stages {
        stage('Build') {
            steps {
                // TODO dev bot build stage
                sh '''
                aws ecr get-login-password --region eu-west-2 | docker login --username AWS --password-stdin 700935310038.dkr.ecr.eu-west-2.amazonaws.com
                docker build -t $IMAGE_NAME:$BUILD_NUMBER -f worker/Dockerfile .
                docker tag $IMAGE_NAME:$BUILD_NUMBER $REGISTRY_URL/$IMAGE_NAME:$BUILD_NUMBER
                docker push $REGISTRY_URL/$IMAGE_NAME:$BUILD_NUMBER

                '''
            }
            post {
               always {
                   sh 'docker image prune -a --filter "until=240h" --force'


               }
            }
        }
    }
}