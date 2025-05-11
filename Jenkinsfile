def cluster = 'devops-onboarding' as Object
def project = 'coral-proj' as Object
def zone = 'us-central1-a' as Object

pipeline {
    docker {
    image 'google/cloud-sdk:slim'
    args '-u root'  # if you need sudo
  }
    }

  environment {
    IMAGE_NAME = 'coral-proj-project'
    PROJECT_ID = 'coral-proj-project-1213'
  }

  stages {

    stage('GCP Auth & GKE Config') {
      steps {
        withCredentials([file(credentialsId: 'GC_KEY', variable: 'GC_KEY')]) {
          sh '''
            sudo apt-get update && sudo apt-get install -y apt-transport-https ca-certificates gnupg curl
echo "deb [signed-by=/usr/share/keyrings/cloud.google.gpg] http://packages.cloud.google.com/apt cloud-sdk main" \
  | sudo tee -a /etc/apt/sources.list.d/google-cloud-sdk.list
curl https://packages.cloud.google.com/apt/doc/apt-key.gpg \
  | sudo apt-key --keyring /usr/share/keyrings/cloud.google.gpg add -
sudo apt-get update && sudo apt-get install -y google-cloud-sdk
            
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
