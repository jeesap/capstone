pipeline {
     agent any
     stages {
         
         stage('Lint HTML') {
              steps {
                  sh 'tidy -q -e *.html'
              }
         }
                  
          
          stage ("lint dockerfile") {
               agent {
                    docker {
                         image 'hadolint/hadolint:latest-debian'
                         }
                     }
                    steps {
                         sh 'hadolint Dockerfile| tee -a hadolint_lint.txt'
                    }
                    post {
                         always {
                             archiveArtifacts 'hadolint_lint.txt'
                         }
                    }
               }  
          
		stage('Upload docker Image')
		{
			steps{
				sh 'docker build . --tag=jees/capstone'
				withCredentials([usernamePassword(credentialsId: 'dockerhub', passwordVariable: 'passwd', usernameVariable: 'username')]) {
					sh 'docker login -u $username -p $passwd'
					sh 'docker push jees/capstone'
				}
			}
		}
          
          stage('Deploy to AWS Kubernetes Cluster') {
             when {
                branch 'master'
            }
              steps {
                  withAWS(region:'us-west-2',credentials:'jenkins') {
                  sh "aws eks --region us-west-2 update-kubeconfig --name capstone"
                  sh "kubectl apply -f deployment.yml"
                  sh "kubectl get nodes"
                  sh "kubectl get deployment"
                  sh "kubectl get pod -o wide"
                  sh "kubectl apply -f indexservices.yml"
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
