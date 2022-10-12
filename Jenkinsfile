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
  stages {
    stage('Run ubuntu') {
      steps {
        container('ubuntu') {
          sh 'lsb_release -a'
        }
      }
    }
  }
}
