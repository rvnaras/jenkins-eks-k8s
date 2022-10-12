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
    DOCKERHUB_CREDENTIALS=credentials('docker')
    registry = "ravennaras/cilist"
  }
  stages {
    stage('clone') {
      steps {
        git 'https://github.com/rvnaras/jenkins-eks-k8s.git'
      }
    }
    stage('test docker') {
      steps {
        script {
          dockerImage = docker.build registry + ":$BUILD_NUMBER"
	}
      } 
    }
  }
}
