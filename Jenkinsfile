pipeline {
  agent any

  stages {
    stage('Dev ECR Login') {
      steps {
        sh 'env'
        sh '$(aws ecr get-login --no-include-email --region ca-central-1)'
      }
    }
stage('Scanning the API') {
      steps {
        sh '''
         sudo apt-get install enscript -y
         sudo apt-get install ghostscript -y
         env
         aws ecr describe-image-scan-findings --repository-name $DOCKER_REPOSITORY --image-id imageTag=$DOCKER_TAG --region ca-central-1
         aws ecr describe-image-scan-findings --repository-name $DOCKER_REPOSITORY --image-id imageTag=$DOCKER_TAG --region ca-central-1 > /tmp/$DOCKER_IMAGE.txt
         enscript -p /tmp/scan.ps /tmp/$DOCKER_IMAGE.txt
         ps2pdf /tmp/scan.psf $DOCKER_IMAGE.pdf
         ls -al /tmp
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
