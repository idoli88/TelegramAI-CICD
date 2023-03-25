pipeline {
    agent {
        docker {
            image '<jenkins-agent-image>'
            args '--user root -v /var/run/docker.sock:/var/run/docker.sock'
        }
    }

    environment {
        APP_ENV = "dev"
    }

    parameters {
        string(name: 'WORKER_IMAGE_NAME')
    }

    stages {
        stage('Checkout') {
            steps {
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: '*/dev']],
                    extensions: [[$class: 'PathRestriction', includedRegions: 'infra/k8s/.*']],
                    userRemoteConfigs: [[url: '<your-repo-url>']]
                ])
            }
        }

        stage('Deploy') {
            steps {
                sh 'kubectl apply -f infra/k8s/worker-deployment.yaml -n ${APP_ENV}'
                sh 'kubectl set image deployment/worker-deployment worker=${WORKER_IMAGE_NAME} -n ${APP_ENV}'
                sh 'kubectl rollout status deployment/worker-deployment -n ${APP_ENV}'
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}
