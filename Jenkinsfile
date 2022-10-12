pipeline {
  agent {
    kubernetes {
      yaml '''
        apiVersion: v1
        kind: Pod
        spec:
          containers:
          - name: docker
            image: docker:latest
	    securityContext:
              allowPrivilegeEscalation: true
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
        script {
	  dockerImage = docker.build registry + ":$BUILD_NUMBER"
        }
	  }
	}
  }
}
