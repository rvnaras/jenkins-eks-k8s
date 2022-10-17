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
          - name: pods
            image: ravennaras/template:eksctl
            securityContext:
              allowPrivilegeEscalation: true
            tty: true
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
    stage('BUILD') {
      steps {
        container('docker') {
            sh '''
              echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin
              docker build -f ./backend/Dockerfile.be -t ravennaras/cilist:bejenkins . --network host 
              docker build -f ./frontend/Dockerfile.fe -t ravennaras/cilist:fejenkins . --network host
            '''
        }
      }
    }
    stage('TEST') {
      steps {
        container('docker') {
            sh '''
              docker run aquasec/trivy image ravennaras/cilist:bejenkins --net=host
              docker run aquasec/trivy image ravennaras/cilist:fejenkins --net=host
              docker push ravennaras/cilist:bejenkins
              docker push ravennaras/cilist:fejenkins
            '''
        }
      }
    }
    stage('DEPLOY') {
      steps {
        container('pods'){
          withAWS(credentials: 'aws'){
            sh '''
              aws configure set default.region us-east-1 && aws configure set aws_access_key_id $AWS_ACCESS_KEY_ID && aws configure set aws_secret_access_key $AWS_SECRET_ACCESS_KEY
              aws eks update-kubeconfig --name=cilsy-eks
              echo login successful
              kubectl apply -f backend.yaml
              kubectl apply -f frontend.yaml
            '''
          }
        }
      }
    }
  }
}
