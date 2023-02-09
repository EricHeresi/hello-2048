pipeline {
    agent any

    stages {
        stage('Image build an push'){
            steps {
                sh 'docker-compose build'
                sh 'docker-compose push'
            }
        }
        stage('AWS_build') {
            steps {
                sshagent(['ssh_amazon']) {
                    sh 'ssh ec2-user@3.250.172.231 docker pull ghcr.io/ericheresi/hello-2048/nginx2048:latest'
                    sh 'ssh ec2-user@3.250.172.231 docker run -dt --rm -p 80:80 ghcr.io/ericheresi/hello-2048/nginx2048:latest'
                }
            }
        }
    }
}
