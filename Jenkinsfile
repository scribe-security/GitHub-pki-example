node {
  withEnv([
    "PATH=./temp/bin:$PATH",
    "LOGICAL_APP_NAME=PKI-Sign-demo-project",
    "APP_VERSION=1.0.2",
    "AUTHOR_NAME=John-Smith", 
    "AUTHOR_EMAIL=jhon@thiscompany.com",
    "AUTHOR_PHONE=555-8426157",
    "SUPPLIER_NAME=Scribe-Security",
    "SUPPLIER_URL=www.scribesecurity.com",
    "SUPPLIER_EMAIL=info@scribesecurity.com",
    "SUPPLIER_PHONE=001-001-0011",
    "PRIVATE_KEY=xxx",
    "SIGNING_CERT=yyy",
    "CA_CERT=xxx"
   
  ]) 
   {
    stage('install') {
      cleanWs()
      sh 'curl -sSfL https://get.scribesecurity.com/install.sh | sh -s -- -b ./temp/bin'
    }
    
    stage('checkout') {
            sh 'git clone -b main --single-branch https://github.com/scribe-security/jenkins-pki-example.git'
            sh 'cd jenkins-pki-example; docker build -t pki-test -f ./orig-Dockerfile .'
     }
    
    stage('bom-git') {
      withCredentials([
        usernamePassword(credentialsId: 'scribe-production-auth-id', usernameVariable: 'SCRIBE_CLIENT_ID', passwordVariable: 'SCRIBE_CLIENT_SECRET'),
        file(credentialsId: 'key-file', variable: 'KEY_FILE'),
        file(credentialsId: 'sig-cert-file', variable: 'SIG_CERT_FILE'),
        file(credentialsId: 'ca-cert-file', variable: 'CA_CERT_FILE')
      ]) 
      
      {
        sh '''
          PRIVATE_KEY=$(cat $KEY_FILE)
          SIGNING_CERT=$(cat $SIG_CERT_FILE)     
          CA_CERT=$(cat $CA_CERT_FILE)
          valint bom git:jenkins-pki-example/. \
            --config jenkins-pki-example/.valint.yaml \
            --components packages,files,dep \
            --context-type jenkins \
            --format attest\
            --output-directory ./scribe/valint \
            -E -U $SCRIBE_CLIENT_ID -P $SCRIBE_CLIENT_SECRET \
            --app-name $LOGICAL_APP_NAME --app-version $APP_VERSION  \
            --author-name $AUTHOR_NAME --author-email AUTHOR_EMAIL --author-phone $AUTHOR_PHONE  \
            --supplier-name $SUPPLIER_NAME --supplier-url $SUPPLIER_URL --supplier-email $SUPPLIER_EMAIL  \
            --supplier-phone $SUPPLIER_PHONE \
            -f '''
      }
    }

    stage('provenance-image') {
      withCredentials([
        usernamePassword(credentialsId: 'scribe-production-auth-id', usernameVariable: 'SCRIBE_CLIENT_ID', passwordVariable: 'SCRIBE_CLIENT_SECRET'),
        file(credentialsId: 'key-file', variable: 'KEY_FILE'),
        file(credentialsId: 'sig-cert-file', variable: 'SIG_CERT_FILE'),
        file(credentialsId: 'ca-cert-file', variable: 'CA_CERT_FILE')
      ])   
      {
      sh '''
          PRIVATE_KEY=$(cat $KEY_FILE)
          SIGNING_CERT=$(cat $SIG_CERT_FILE)     
          CA_CERT=$(cat $CA_CERT_FILE)
          valint slsa pki-test:latest \
            --config jenkins-pki-example/.valint.yaml \
            --format attest\
            --context-type jenkins \
            --output-directory ./scribe/valint \
            -E -U $SCRIBE_CLIENT_ID -P $SCRIBE_CLIENT_SECRET \
            --app-name $LOGICAL_APP_NAME --app-version $APP_VERSION  \
            -f '''
      }
    }

     stage('bom-image') {
      withCredentials([
        usernamePassword(credentialsId: 'scribe-production-auth-id', usernameVariable: 'SCRIBE_CLIENT_ID', passwordVariable: 'SCRIBE_CLIENT_SECRET'),
        file(credentialsId: 'key-file', variable: 'KEY_FILE'),
        file(credentialsId: 'sig-cert-file', variable: 'SIG_CERT_FILE'),
        file(credentialsId: 'ca-cert-file', variable: 'CA_CERT_FILE')
      ])   
      {
      sh '''
          PRIVATE_KEY=$(cat $KEY_FILE)
          SIGNING_CERT=$(cat $SIG_CERT_FILE)     
          CA_CERT=$(cat $CA_CERT_FILE)
          valint bom pki-test:latest \
            --config jenkins-pki-example/.valint.yaml \
            --context-type jenkins \
            --format attest\
            --output-directory ./scribe/valint \
            -E -U $SCRIBE_CLIENT_ID -P $SCRIBE_CLIENT_SECRET \
            --app-name $LOGICAL_APP_NAME --app-version $APP_VERSION  \
            --author-name $AUTHOR_NAME --author-email AUTHOR_EMAIL --author-phone $AUTHOR_PHONE  \
            --supplier-name $SUPPLIER_NAME --supplier-url $SUPPLIER_URL --supplier-email $SUPPLIER_EMAIL  \
            --supplier-phone $SUPPLIER_PHONE \
            -f '''
      }
    }

    stage('bom-image-verify') {
      withCredentials([
        usernamePassword(credentialsId: 'scribe-production-auth-id', usernameVariable: 'SCRIBE_CLIENT_ID', passwordVariable: 'SCRIBE_CLIENT_SECRET'),
        file(credentialsId: 'key-file', variable: 'KEY_FILE'),
        file(credentialsId: 'sig-cert-file', variable: 'SIG_CERT_FILE'),
        file(credentialsId: 'ca-cert-file', variable: 'CA_CERT_FILE')
      ])   
      {
      sh '''
          PRIVATE_KEY=$(cat $KEY_FILE)
          SIGNING_CERT=$(cat $SIG_CERT_FILE)     
          CA_CERT=$(cat $CA_CERT_FILE)
          valint verify pki-test:latest \
            --config jenkins-pki-example/.valint.yaml \
            --output-directory ./scribe/valint \
            --context-type jenkins \
            -E -U $SCRIBE_CLIENT_ID -P $SCRIBE_CLIENT_SECRET \
           '''
      }
    }
     
  }
}
