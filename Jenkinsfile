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
        sh 'echo skipped'
      }
    }
    stage('test docker') {
      steps {
        sh 'kubectl apply -f backend.yaml'
      } 
    }
  }
}
