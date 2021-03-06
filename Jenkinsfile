pipeline {
     agent any
     stages {
         
         stage('Lint HTML') {
              steps {
                  sh 'tidy -q -e *.html'
              }
         }
                  
         stage('Upload to AWS S3') {
            when {
               branch 'master'
            }
              steps {
                  withAWS(region:'us-west-2',credentials:'jenkins') {
                  sh 'echo "Uploading content with AWS creds"'
                      s3Upload(pathStyleAccessEnabled: true, payloadSigningEnabled: true, file:'index.html', bucket:'udacity')
                  }
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
          
         stage('Build Docker Image') {
             when {
                branch 'master'
            }
              steps { 
                  sh 'docker build --tag=capstone .'
              }
         }
         stage('upload image to Dockerhub') {
              steps { 
                  script {
                  withDockerRegistry([ credentialsId: "dockerhub", url: "" ]){
                  sh "docker tag capstone jees/capstone"
                  sh 'docker push jees/capstone'
                  }
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
                    echo 'Cleaning up...'
                    sh "docker system prune"
              }
        } 
        
     }
}
