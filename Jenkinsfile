pipeline {
  agent {
    label 'myapp-jenkins-agent'  // make sure this matches the working label
  }

  environment {
    IMAGE_NAME = 'coral-proj-project'
    PROJECT_ID = 'coral-proj-project-1213'
    CLUSTER_NAME = 'devops-onboarding'
    GKE_ZONE = 'us-central1-a'
  }

  stages {
    stage('Install Google Cloud SDK') {
      steps {
        sh '''
          echo "Installing Google Cloud SDK"
          curl -sSL https://sdk.cloud.google.com | bash
          source "$HOME/google-cloud-sdk/path.bash.inc"
          gcloud version
        '''
      }
    }

    stage('GCP Auth & GKE Config') {
      steps {
        withCredentials([file(credentialsId: 'GC_KEY', variable: 'GC_KEY')]) {
          sh '''
            echo "Activating GCP service account"
            source "$HOME/google-cloud-sdk/path.bash.inc"
            gcloud auth activate-service-account --key-file=$GC_KEY

            echo "Getting GKE cluster credentials"
            gcloud container clusters get-credentials $CLUSTER_NAME \
              --zone $GKE_ZONE \
              --project $PROJECT_ID
          '''
        }
      }
    }

    stage('Checkout Repo') {
      steps {
        git branch: 'main',
            credentialsId: 'github',
            url: 'https://github.com/Amirn88/coral-proj-project-1213'
      }
    }

    stage('Install Ingress Controller via Helm') {
      steps {
        sh '''
          echo "Adding ingress-nginx Helm repo"
          helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
          helm repo update

          echo "Creating ingress-nginx namespace (if not exists)"
          kubectl create namespace ingress-nginx || true

          echo "Installing ingress-nginx via Helm"
          helm install ingress-nginx ingress-nginx/ingress-nginx \
            --namespace ingress-nginx \
            --create-namespace

          echo "Checking LoadBalancer IP"
          kubectl get svc ingress-nginx-controller -n ingress-nginx
        '''
      }
    }
  }
}
