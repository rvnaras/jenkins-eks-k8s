pipeline {
  agent {
    kubernetes {
      yaml '''
        apiVersion: v1
        kind: Pod
        spec:
          containers:
          - name: pods
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
    stage('login to dockerhub') {
      steps {
        container('pods') {
            sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
        }
      }
    }
    stage('build and push docker image') {
      steps {
        container('pods') {
            sh '''
              docker build -f ./backend/Dockerfile.be -t ravennaras/cilist:bejenkins . --network host 
              docker push ravennaras/cilist:bejenkins
              docker build -f ./frontend/Dockerfile.fe -t ravennaras/cilist:fejenkins . --network host
              docker push ravennaras/cilist:fejenkins
            '''
        }
      }
    }
    stage('login to aws cluster') {
      steps {
        container('pods'){
          withAWS(credentials: 'aws'){
            sh '''
              apt get update -y
            '''
          }
        }
      }
    }
    stage('deploy to k8s cluster') {
      steps {
        container('pods'){
          sh '''
            echo deploy
          '''
        }
      }
    }
  }
}
