pipeline {
 agent any

 environment {
   DOCKERHUB_CREDENTIALS=credentials('dockerhub')
 }

stages {
   stage('installing k8s') {
     steps {
       sh 'curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"'
       sh 'sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl'
       sh 'kubectl version --client'
     }
   }
	
   stage('testing k8s') {
     steps {
       sh 'kubectl get nodes'
     }
   }

   stage('cloning repository') {
     steps {
       sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
       git 'https://github.com/rvnaras/jenkins-eks-k8s.git'
     }
   }
   
   stage('build and push docker image database') {
     steps {
       dir('database') {
         sh 'docker build . -t ravennaras/cilist:dbjenkins'
	 sh 'docker push ravennaras/cilist:dbjenkins'
       }
     }
   }

   stage('build and push docker image backend') {
     steps {
       dir('backend') {
         sh 'docker build . -t ravennaras/cilist:bejenkins'
	 sh 'docker push ravennaras/cilist:bejenkins'
       }
     }
   }
    
   stage('build docker image frontend') {
     steps {
       dir('frontend') {
         sh 'docker build . -t ravennaras/cilist:fejenkins'
	 sh 'docker push ravennaras/cilist:fejenkins'
       }
     }
   }

   stage('deploy to pod') {
     steps {
	   sh 'kubectl apply -f namespace.yaml'
	   sh 'kubectl apply -f secret.yaml'
	   sh 'kubectl apply -f configmap-be.yaml'
	   sh 'kubectl apply -f configmap-fe.yaml'
	   sh 'kubectl apply -f backend-svc.yaml'
	   sh 'kubectl apply -f frontend-svc.yaml'
	   sh 'kubectl apply -f backend.yaml'
	   sh 'kubectl apply -f frontend.yaml'
	   sh 'kubectl apply -f ingress.yaml'
     }
   }
 }
}
