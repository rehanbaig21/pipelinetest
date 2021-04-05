pipeline {
  agent any

  stages {
stage('Scanning the API') {
      steps {
        sh '''
         
         /usr/local/bin/aws ecr describe-image-scan-findings --repository-name $DOCKER_REPOSITORY --image-id imageTag=$DOCKER_TAG --region ca-central-1 > ./$DOCKER_TAG.txt
         sudo enscript -p /tmp/$DOCKER_TAG.ps ./$DOCKER_TAG.txt
         sudo ps2pdf /tmp/$DOCKER_TAG.ps ./$DOCKER_TAG.pdf
         ls -al ./
         '''

      }
    }
 

  stage('email') {
  steps {
    
 sh '''
ls -al
aws s3 cp s3://rehantestbucket/rawemailmessage.json . --region ca-central-1
sed -i 's/{ATTACHMENT}/'"$DOCKER_TAG.pdf"'/g' ./rawemailmessage.json
sed -i 's/{SUBJECT}/'"$DOCKER_TAG"-Vuln-Scan-Result'/g' ./rawemailmessage.json
cat ./rawemailmessage.json
aws ses send-raw-email --cli-binary-format raw-in-base64-out --raw-message file://rawemailmessage.json --region ca-central-1
''' 
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
