pipeline {
 agent any

 environment {
   DOCKERHUB_CREDENTIALS=credentials('dockerhub')
 }

 stages {
   stage('cloning repository') {
     steps {
       git 'https://github.com/rvnaras/jenkins-eks-k8s.git'
     }
   }

   stage('login docker') {
     steps {
       sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
     }
   }

   stage('build docker image database') {
     steps {
       dir('database') {
         sh 'docker build . -t ravennaras/cilist:dbjenkins'
       }
     }
   }
   
   stage('pushing docker image database') {
     steps {
       dir('database') {
         sh 'docker push ravennaras/cilist:dbjenkins'
       }
     }
   }

   stage('build docker image backend') {
     steps {
       dir('backend') {
         sh 'docker build . -t ravennaras/cilist:bejenkins'
       }
     }
   }
   
   stage('pushing docker image backend') {
     steps {
       dir('backend') {
         sh 'docker push ravennaras/cilist:bejenkins'
       }
     }
   }
   
   stage('build docker image frontend') {
     steps {
       dir('frontend') {
         sh 'docker build . -t ravennaras/cilist:fejenkins'
       }
     }
   }
   
   stage('pushing docker image frontend') {
     steps {
       dir('frontend') {
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
