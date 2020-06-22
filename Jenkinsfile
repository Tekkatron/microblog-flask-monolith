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
          }
         steps {
			withVault(configuration: [timeout: 60, vaultCredentialId: 'vault-root', vaultUrl: 'http://35.192.106.11'], vaultSecrets: [[path: 'secret/mailflask/app', secretValues: [[envVar: 'usuario', vaultKey: 'username'], [envVar: 'clave', vaultKey: 'password']]]]) {
		 createFlaskEnv()
		 } 
            sh "docker build . -t gcr.io/nutresa-165613/flask-vault-example:latest" 
            sh "docker push gcr.io/nutresa-165613/flask-vault-example:latest" 
         }
      }
      stage('Despliegue') {
         steps {
            sh "gcloud container clusters get-credentials pr-devopscl --zone us-central1-c --project nutresa-prod"
			sh "kubectl apply -f app-deployment.yaml"
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
    sh 'echo MAIL_USERNAME=$usuario >> .flaskenv'
    sh 'echo MAIL_PASSWORD=$clave >> .flaskenv'
    sh 'echo FLASK_DEBUG=1 >> .flaskenv'
    sh 'echo FLASK_ENV=development >> .flaskenv'
}