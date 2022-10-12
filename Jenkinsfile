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
 environment {
   DOCKERHUB_CREDENTIALS=credentials('dockerhub')
 }

stages {
   stage('cloning repo') {
     steps {
	   container('ubuntu'){
       sh 'sudo apt update -y && sudo apt upgrade -y'
       sh 'sudo apt install docker.io'
	   sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
	   sh 'git clone https://github.com/rvnaras/jenkins-eks-k8s.git'
	   }
	   git 'https://github.com/rvnaras/jenkins-eks-k8s.git'
	 }
   }
 
   stage('build and push docker image backend') {
     steps {
	   container('ubuntu'){
           sh 'sudo docker image build -f backend/Dockerfile.be -t ravennaras/cilist:dbjenkins .'
	       sh 'sudo docker image push ravennaras/cilist:dbjenkins'
	   }
     }
   }
   
   stage('build and push docker image frontend') {
     steps {
	   container('ubuntu'){
           sh 'sudo docker image build -f frontend/Dockerfile.fe -t ravennaras/cilist:fejenkins .'
	       sh 'sudo docker image push ravennaras/cilist:fejenkins'
	   }
     }
   }

   stage('deploying app to kubernetes cluster') {
      steps {
        script {
          kubernetesDeploy(configs: "backend.yaml")
		  kubernetesDeploy(configs: "frontend.yaml")
        }
      }
    }
  }
}
