pipeline {
    agent any
    environment {
	BUILD_NUMBER = '2'
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
                sh 'docker-compose build'
		sh 'git tag 1.0.${BUILD_NUMBER}'
		withCredentials([gitUsernamePassword(credentialsId: 'github-auth', gitToolName: 'Default')]) {
		    sh 'git push --tags'
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
