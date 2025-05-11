def cluster = 'devops-onboarding' as Object
def project = 'coral-proj' as Object
def zone = 'us-central1-a' as Object

pipeline {
  agent any

  environment {
    IMAGE_NAME = 'coral-proj-project'
    PROJECT_ID = "${project}"
  }

  stages {
    stage('Checkout Repo') {
      steps {
        git branch: 'main',
            credentialsId: 'github',
            url: 'https://github.com/Amirn88/coral-proj-project-1213'
      }
    }

    stage('GCP Auth & Get GKE Credentials') {
      steps {
        withCredentials([file(credentialsId: 'coral-prod', variable: 'GC_KEY')]) {
          sh """
            echo "🔐 Activating GCP service account"
            gcloud auth activate-service-account --key-file=$GC_KEY

            echo "🔧 Getting GKE credentials for cluster: ${cluster}"
            gcloud container clusters get-credentials ${cluster} \
              --zone ${zone} \
              --project ${project}
          """
        }
      }
    }

    stage('Install Ingress Controller via Helm') {
      steps {
        sh """
          echo "📦 Adding ingress-nginx Helm repo"
          helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
          helm repo update

          echo "📁 Creating namespace ingress-nginx (if not exists)"
          kubectl create namespace ingress-nginx || true

          echo "🚀 Installing or upgrading ingress-nginx via Helm"
          helm upgrade --install ingress-nginx ingress-nginx/ingress-nginx \
            --namespace ingress-nginx \
            --create-namespace

          echo "🔎 Verifying LoadBalancer IP"
          kubectl get svc ingress-nginx-controller -n ingress-nginx
        """
      }
    }
  }

  post {
    always {
      echo "🧾 Pipeline finished."
    }
    success {
      echo "✅ Ingress controller setup successful!"
    }
    failure {
      echo "❌ Something went wrong during the deployment."
    }
  }
}
