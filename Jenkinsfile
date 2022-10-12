pipeline {
  agent {
    kubernetes {
      yaml '''
        apiVersion: v1
        kind: Pod
        spec:
          containers:
          - name: docker
            image: docker:dind
            securityContext:
              allowPrivilegeEscalation: true
            command:
            - cat
            tty: true
            volumeMounts:
            - name: dockersock
              mountPath: 'var/run/docker.sock'
          volumes:
          - name: dockersock
            hostPath:
              path: /var/run/docker.sock
        '''
    }
  }
  environment{
    DOCKERHUB_CREDENTIALS=credentials('docker')
    registry = "ravennaras/cilist"
  }
  stages {
    stage('build docker image') {
      steps {
        container('docker') {
            sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
            sh 'docker build -f ./backend/Dockerfile.be -t ravennaras/cilist:bejenkins . '
            sh 'docker push ravennaras/cilist:bejenkins'
            sh 'docker build -f ./frontend/Dockerfile.fe -t ravennaras/cilist:fejenkins . '
            sh 'docker push ravennaras/cilist:fejenkins'
        }
      }
    }
  }
}
