def cluster = 'devops-onboarding' as java.lang.Object
def project = 'coral-proj' as java.lang.Object
def zone = 'us-central1-a' as java.lang.Object

pipeline {
   agent any

    environment {
        IMAGE_NAME = 'coral-proj-project'
        PROJECT_ID = "coral-proj-project"
        
    }
    }
     stages {
        stage('setup') {
            steps {
                withCredentials([file(credentialsId: 'coral-prod-sa', variable: 'GC_KEY')]) {
                    sh("gcloud auth activate-service-account --key-file=${GC_KEY}")
                    sh("gcloud container clusters get-credentials ${cluster} --zone ${zone} --project ${project}")
                }
            }
        }
        stage('checkout') {
            steps {
                git branch: 'main', credentialsId: 'github', url: "https://github.com/Amirn88/coral-proj-project-1213"
            }
        }
        }
        stage('Ingress-Helm-Install') {
            steps {
                sh """
                     # Add the NGINX Ingress Helm Repo
                     helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
                     helm repo update

                     # Create a Namespace ingress-nginx
                     kubectl create namespace ingress-nginx

                     # Install the Ingress Controller
                     helm install ingress-nginx ingress-nginx/ingress-nginx \
                       --namespace ingress-nginx \
                       --create-namespace

                     # LoadBalancer IP  
                     kubectl get svc ingress-nginx-controller -n ingress-nginx  
                    
                """
            }
        }

    
    }
}
