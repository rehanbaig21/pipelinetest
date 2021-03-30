pipeline {
  agent any

  stages {
stage('Scanning the API') {
      steps {
        sh '''
         aws --version
         
         /usr/local/bin/aws ecr describe-image-scan-findings --repository-name $DOCKER_REPOSITORY --image-id imageTag=$DOCKER_TAG --region ca-central-1
         /usr/local/bin/aws ecr describe-image-scan-findings --repository-name $DOCKER_REPOSITORY --image-id imageTag=$DOCKER_TAG --region ca-central-1 > /tmp/$DOCKER_TAG.txt
         sudo enscript -p /tmp/$DOCKER_TAG.ps /tmp/$DOCKER_TAG.txt
         sudo ps2pdf /tmp/$DOCKER_TAG.ps /tmp/$DOCKER_TAG.pdf
         ls -al /tmp
         '''
         zipFile: $DOCKER_TAG.pdf, archive: false, dir: '/tmp'

      }
    }
  
  stage('email') {
  steps {
  emailext attachmentsPattern: '$DOCKER_TAG.pdf', body: '''${SCRIPT, template="groovy-html.template"}''', 
    subject: "$DOCKER_IMAGE Vuln Scan Result",
    attachment: '/tmp/$DOCKER_TAG.pdf'
    mimeType: 'text/html',to: "mirza.baig@applyboard.com"
    
 
  }
  }
  }
  environment {
     CONTEXT = 'dev'
     DOCKER_REGISTRY_DEV = '969776126790.dkr.ecr.ca-central-1.amazonaws.com'
     DOCKER_REPOSITORY = 'applyproof/applyproof-portal'
     DOCKER_TAG = "${params.image_tag}"
     DOCKER_IMAGE = "$DOCKER_REGISTRY_DEV/$DOCKER_REPOSITORY:$DOCKER_TAG"
     AWS_DEFAULT_REGION = 'ca-central-1'
   }
   parameters {
     string(name: 'image_tag', description: 'Which image tag would you like to deploy?')
   }
}
