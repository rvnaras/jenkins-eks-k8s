pipeline {
  agent {
    kubernetes {
      yaml '''
        apiVersion: v1
        kind: Pod
        spec:
          containers:
          - name: ubuntu
            image: ubuntu:latest
            command:
            - cat
            tty: true
        '''
    }
  }
  environment{
    DOCKERHUB_CREDENTIALS=credentials('dockerhub')
  }
  stages {
    stage('cloning repo') {
      steps {
        container('ubuntu') {
          sh 'sudo apt update -y && sudo apt upgrade -y'
		      sh 'sudo apt install docker.io'
		      sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
		      sh 'git clone https://github.com/rvnaras/jenkins-eks-k8s.git'
        }
		  git 'https://github.com/rvnaras/jenkins-eks-k8s.git'
      }
    }
  }
}
