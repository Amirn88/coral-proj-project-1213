pipeline {
  agent {
   docker {
      image 'amirn88/jenkins-gcp-agent:latest'
      args '-v /var/run/docker.sock:/var/run/docker.sock' // required if you use Docker inside
      registryCredentialsId 'dockerhub-creds'
    }
  }

  environment {
    IMAGE_NAME = 'coral-proj-project'
    PROJECT_ID = 'coral-proj-project-1213'
    CLUSTER_NAME = 'devops-onboarding'
    GKE_ZONE = 'us-central1-a'
  }

  stages {
    stage('Verify Tools') {
      steps {
        sh '''
          echo "✅ Verifying tool versions:"
          python3 --version
          gcloud version
          kubectl version --client --short
          helm version --short
          jq --version
          docker --version
        '''
      }
    }

    stage('GCP Auth & GKE Config') {
      steps {
        withCredentials([file(credentialsId: 'GC_KEY', variable: 'GC_KEY')]) {
          sh '''
            echo "🔐 Authenticating with GCP"
            gcloud auth activate-service-account --key-file=$GC_KEY

            echo "🔧 Fetching GKE credentials"
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
          echo "➕ Adding ingress-nginx Helm repo"
          helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
          helm repo update

          echo "📦 Creating namespace if not exists"
          kubectl create namespace ingress-nginx || true

          echo "🚀 Installing ingress-nginx via Helm"
          helm install ingress-nginx ingress-nginx/ingress-nginx \
            --namespace ingress-nginx \
            --create-namespace || true

          echo "🔍 Checking LoadBalancer service"
          kubectl get svc ingress-nginx-controller -n ingress-nginx
        '''
      }
    }
  }
}
