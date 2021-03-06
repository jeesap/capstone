pipeline {
     agent any
     stages {
         
         stage('Lint HTML') {
              steps {
                  sh 'tidy -q -e *.html'
              }
         }
                  
          
    		stage("Lint Dockerfile") {
			steps {
      		              sh 'sudo /usr/bin/hadolint Dockerfile'
			}
		      	} 
          
	     
		stage('Build and Upload docker Image')
		{
			steps{
				sh 'docker build . --tag=jeesap/capstone'
				withCredentials([usernamePassword(credentialsId: 'dockerhub', passwordVariable: 'passwd', usernameVariable: 'username')]) {
					sh 'docker login -u $username -p $passwd'
					sh 'docker push jeesap/capstone:latest'
				}
			}
		}
          
          stage('Deploy to AWS Kubernetes Cluster') {
                  steps {
                  withAWS(region:'us-west-2',credentials:'aws') {
                  sh "aws eks --region us-west-2 update-kubeconfig --name capstone"
                  sh "kubectl apply -f deployment.yml"
                  sh "kubectl get nodes"
                  sh "kubectl get deployment"
                  sh "kubectl get pod -o wide"
                  sh "kubectl apply -f services.yml"
                  sh "kubectl get services"
                    
                  }
              }
         }
          
         stage("Cleaning up") {
              steps{
                    echo 'Cleaning up....'
                    sh "docker system prune"
              }
        } 
        
     }
}
