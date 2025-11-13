pipeline {
    agent any

    environment {
        PROJECT_ID = "amiable-archive-473111-d4"
        REGION = "us-central1"
        CLUSTER = "devops-cluster"
        ZONE = "us-central1-a"
        REPO = "us-central1-docker.pkg.dev/amiable-archive-473111-d4/devops-repo"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Images') {
            steps {
                sh 'docker build -t $REPO/backend:v1 ./backend'
                sh 'docker build -t $REPO/frontend:v1 ./frontend'
            }
        }

        stage('Push Images to Artifact Registry') {
            steps {
                sh 'gcloud auth configure-docker $REGION-docker.pkg.dev -q'
                sh 'docker push $REPO/backend:v1'
                sh 'docker push $REPO/frontend:v1'
            }
        }

        stage('Deploy to GKE') {
            steps {
                sh 'gcloud container clusters get-credentials $CLUSTER --zone $ZONE --project $PROJECT_ID'
                sh 'kubectl apply -f k8s/'
            }
        }
    }
}
