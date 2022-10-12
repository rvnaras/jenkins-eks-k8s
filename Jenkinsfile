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
    
    stage('deploy to k8s') {
      steps {
        script {
          kubernetesDeploy(configs: "backend.yaml", kubeconfigId: "k8s")
	      kubernetesDeploy(configs: "frontend.yaml, kubeconfigId: "k8s"")
        }
      }
    }
  }
}
