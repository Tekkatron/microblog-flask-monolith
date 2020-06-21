pipeline {
   agent {
       label "poc2"
   }

   stages {
      stage('Obtener Codigo') {
         steps {
            git 'https://github.com/Tekkatron/microblog-flask-monolith.git'
         }
      }
      stage('Build') {
          environment {
            MAIL_SERVER = 'smtp.mailtrap.io'
            MAIL_PORT = '587'
            MAIL_USE_TLS     = '1'
            MAIL_CREDS = credentials("MAIL_SECRECT_VAULT")
          }
         steps {
            createFlaskEnv() 
            sh "docker build . -t gcr.io/nutresa-165613/flask-vault-example:latest" 
            sh "docker push gcr.io/nutresa-165613/flask-vault-example:latest" 
         }
      }
      stage('Despliegue') {
         steps {
            sh "gcloud container clusters get-credentials poc-vault --zone us-central1-a --project nutresa-165613"
			
         }
      }
   }
}

def createFlaskEnv(){
    sh 'echo FLASK_APP=$FLASK_APP > .flaskenv'
    sh 'echo FLASK_DEBUG=$FLASK_DEBUG >> .flaskenv'
    sh 'echo MAIL_SERVER=$MAIL_SERVER >> .flaskenv'
    sh 'echo MAIL_PORT=$MAIL_PORT >> .flaskenv'
    sh 'echo MAIL_USE_TLS=$MAIL_USE_TLS >> .flaskenv'
    sh 'echo MAIL_USERNAME=$MAIL_CREDS_USR >> .flaskenv'
    sh 'echo MAIL_PASSWORD=$MAIL_CREDS_PSW >> .flaskenv'
}