pipeline {
    agent {
        docker {
            // TODO build & push your Jenkins agent image, place the URL here
            image '700935310038.dkr.ecr.eu-west-2.amazonaws.com/eli-abukrat-jenkins-agent:latest'
            args  '--user root -v /var/run/docker.sock:/var/run/docker.sock'
        }
    }

    environment {
        APP_ENV = "prod"
    }




    parameters {
        string(name: 'BOT_IMAGE_NAME')
    }

    stages {
        stage('Bot Deploy') {
            steps {
                echo "${BOT_IMAGE_NAME}"

                    sh '''
                    aws eks update-kubeconfig --region eu-north-1 --name k8s-main
                    # apply the configurations to k8s cluster
                    sed -i "s|BOT_IMAGE|$BOT_IMAGE_NAME|g" infra/k8s/botprod.yaml
                    kubectl apply  -f infra/k8s/botprod.yaml --namespace idot


                    '''

            }
        }
    }
}