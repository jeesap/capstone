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
                      s3Upload(pathStyleAccessEnabled: true, payloadSigningEnabled: true, file:'index.html', bucket:'udapeople-jees1979')
                  }
              }
         }
