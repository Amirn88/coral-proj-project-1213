pipeline {
  agent any

  environment {
    CLUSTER = 'devops-onboarding'
    PROJECT = 'coral-proj'
    ZONE = 'us-central1-a'
    GCLOUD_SDK_DIR = "${WORKSPACE}/google-cloud-sdk"
    PATH = "${GCLOUD_SDK_DIR}/bin:$PATH"
    HELM_VERSION = '3.14.0'
    JENKINS_NAMESPACE = 'jenkins'
  }

  stages {
    stage('Checkout Repo') {
      steps {
        git branch: 'main',
            credentialsId: 'github',
            url: 'https://github.com/Amirn88/coral-proj-project-1213'
      }
    }

    stage('Install Tools') {
      steps {
        sh '''#!/bin/bash -e
          echo "🛠 Installing Required Tools..."
          
          # Install Google Cloud SDK
          if [ ! -d "${GCLOUD_SDK_DIR}" ]; then
            echo "Installing Google Cloud SDK..."
            curl -sSL https://dl.google.com/dl/cloudsdk/channels/rapid/downloads/google-cloud-sdk-460.0.0-linux-x86_64.tar.gz -o gcloud.tar.gz
            tar -xzf gcloud.tar.gz
            rm gcloud.tar.gz
            ./google-cloud-sdk/install.sh --quiet
            ./google-cloud-sdk/bin/gcloud components install kubectl gke-gcloud-auth-plugin --quiet
          fi
          
          # Install Helm
          echo "Installing Helm ${HELM_VERSION}..."
          curl -sSL https://get.helm.sh/helm-v${HELM_VERSION}-linux-amd64.tar.gz | tar xz
          sudo mv linux-amd64/helm /usr/local/bin/
          rm -rf linux-amd64
          
          # Verify installations
          gcloud version
          helm version
          kubectl version --client
        '''
      }
    }

    stage('GCP Auth & Cluster Setup') {
      steps {
        withCredentials([file(credentialsId: 'coral-prod-sa', variable: 'GC_KEY')]) {
          sh '''#!/bin/bash -e
            echo "🔐 Authenticating to GCP"
            gcloud auth activate-service-account --key-file="${GC_KEY}" --project="${PROJECT}"
            gcloud config set project "${PROJECT}"
            
            echo "🔧 Connecting to GKE Cluster"
            gcloud container clusters get-credentials "${CLUSTER}" \
              --zone "${ZONE}" \
              --project "${PROJECT}"
              
            echo "🔄 Configuring Docker & Helm"
            gcloud auth configure-docker --quiet
            helm repo add jenkins https://charts.jenkins.io
            helm repo update
            
            echo "✅ Cluster Verification"
            kubectl cluster-info
            kubectl create namespace ${JENKINS_NAMESPACE} --dry-run=client -o yaml | kubectl apply -f -
          '''
        }
      }
    }

    stage('Deploy Jenkins with Helm') {
      steps {
        sh '''#!/bin/bash -e
          echo "🚀 Deploying Jenkins with Helm"
          
          # Create values file with minimal configuration
          cat > jenkins-values.yaml <<EOF
          controller:
            resources:
              requests:
                cpu: "500
