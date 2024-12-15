// Jenkinsfile (Declarative Pipeline)
pipeline {
    agent any
    environment {
        DOCKERHUB_CREDS = credentials('dockerhub')
	
    }

    stages {
        stage('Docker Image Build') {
            steps {
                echo 'Building Docker Image...'
                sh 'docker build --tag christieseery25/cw2-server:1.0 .'
                echo 'Docker Image Built successfully!'
            }
        }

        stage('Test Docker Image') {
            steps {
                echo 'Testing Docker Image...'
                sh '''
                    docker image inspect christieseery25/cw2-server:1.0
                    docker run --name test-container -p 8081:8080 -d christieseery25/cw2-server:1.0
                    docker ps
                    docker stop test-container
                    docker rm test-container
                '''
            }
        }

        stage('DockerHub Login') {
            steps {
                sh 'echo $DOCKERHUB_CREDS_PSW | docker login -u $DOCKERHUB_CREDS_USR --password-stdin'
            }
        }

        stage('DockerHub Image Push') {
            steps {
		echo 'Pushing image to DockerHub'
                sh 'docker push christieseery25/cw2-server:1.0'
            }
        }

        stage('Deploy') {
            steps {
                sshagent(['jenkins-k8s-ssh-key']) {
                    echo 'Updating the deployment with the new image'
                    sh 'kubectl set image deployments/kubernetes-task2 cw2-server=christieseery25/cw2-server:v2'
                    sh 'kubectl rollout status deployments/kubernetes-task2'
                   
                }
            }
        }
    }
}
									
