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
                chmod +x bash-file.sh
                sh bash-file.sh
                '''
            }
        }
    }
}
