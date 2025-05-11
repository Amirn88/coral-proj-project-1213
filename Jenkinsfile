pipeline {
    agent any

    stages {
        stage('Checkout Stage') {
            steps {
                git branch: 'main', url: 'https://github.com/Amirn88/coral-proj-project-1213.git'
            }
        }

        stage('Script Usage') {
            steps {
                sh '''
                    if [ -f bash-file.sh ]; then
                        chmod +x bash-file.sh
                        ./bash-file.sh
                    else
                        echo "Script bash-file.sh not found!"
                        exit 1
                    fi
                '''
            }
        }
    }
}
