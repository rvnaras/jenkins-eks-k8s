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
          sh 'apt update -y && apt upgrade -y'
          sh 'apt install docker.io -y'
          sh 'systemctl start docker'
          sh 'docker version'
	  sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
	  sh 'git clone https://github.com/rvnaras/jenkins-eks-k8s.git'
        }
      }
    }
    stage('build and push docker image backend') {
      steps {
        container('ubuntu') {
          sh 'cd jenkins-eks-k8s'
          sh 'docker image build -f backend/Dockerfile.be -t ravennaras/cilist:dbjenkins .'
	  sh 'docker image push ravennaras/cilist:dbjenkins'
        }
      }
    }
  }
}
