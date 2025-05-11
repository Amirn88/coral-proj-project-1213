pieline{
    agent any
    stages{
        stage('checkout stage'){
            steps{
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/Amirn88/coral-proj-project-1213.git']])
            }
        }
        stage('script usage'){
            steps{
                sh'''
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
