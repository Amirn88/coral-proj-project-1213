pipeline {
  agent {
        kubernetes {
            containerTemplate {
                name 'default'
                namespace 'default'
                image 'gcr.io/google.com/cloudsdktool/google-cloud-cli:stable'
                command 'sleep'
                args 'infinity'
            }
            defaultContainer 'default'
        }
    }



  stages {
    stage('GCP Auth & GKE Config') {
      steps {
        withCredentials([file(credentialsId: 'GC_KEY', variable: 'GC_KEY')]) {
          script {
            docker.image('gcr.io/google.com/cloudsdktool/google-cloud-cli:stable')
                  .inside('-v /var/run/docker.sock:/var/run/docker.sock') {
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
        script {
          docker.image('gcr.io/google.com/cloudsdktool/google-cloud-cli:stable')
                .inside('-v /var/run/docker.sock:/var/run/docker.sock') {
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
  }
}
