def cluster = 'devops-onboarding' as Object
def project = 'coral-proj' as Object
def zone = 'us-central1-a' as Object

pipeline {
  agent any

  environment {
    IMAGE_NAME = 'coral-proj-project'
    PROJECT_ID = 'coral-proj-project'
  }

  stages {

    stage('GCP Auth & GKE Config') {
      steps {
        withCredentials([file(credentialsId: 'coral-proj', variable: 'GC_KEY')]) {
          sh '''
            echo " Activating GCP service account"
            gcloud auth activate-service-account --key-file=$GC_KEY

            echo " Getting GKE cluster credentials"
            gcloud container clusters get-credentials devops-onboarding \
              --zone us-central1-a \
              --project coral-proj
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
          echo " Adding ingress-nginx Helm repo"
          helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
          helm repo update

          echo " Creating ingress-nginx namespace (if not exists)"
          kubectl create namespace ingress-nginx || true

          echo " Installing ingress-nginx via Helm"
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
