pipeline {
    agent any
    environment {
	BUILD_NUMBER = '11'
    }
    stages {
	stage('Git Login'){
	    steps {
		withCredentials([string(credentialsId: 'github-token', variable: 'GIT_TOKEN')]) {
		    sh 'echo $GIT_TOKEN | docker login ghcr.io -u EricHeresi --password-stdin'
		}
	    }
	}
        stage('Image generation'){
            steps {
                sh 'MY_TAG=1.0.${BUILD_NUMBER} docker-compose build'
		sh 'git tag 1.0.${BUILD_NUMBER}'
		sshagent(['github-ssh']) {
		    sh 'git push git@github.com:EricHeresi/hello-2048.git --tags'
		}
                sh 'docker-compose push'
            }
        }
        stage('AWS deploy') {
            steps {
                sshagent(['ssh_amazon']) {
                    sh 'ssh ec2-user@3.250.172.231 docker pull ghcr.io/ericheresi/hello-2048/nginx2048:latest'
                    sh 'ssh ec2-user@3.250.172.231 docker run -dt --rm -p 80:80 ghcr.io/ericheresi/hello-2048/nginx2048:latest'
                }
            }
        }
    }
}
