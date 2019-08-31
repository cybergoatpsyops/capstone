pipeline {
    agent any
    stages {
      stage('Build') {
        steps {
	    echo 'Building..'
	}
      }
      stage('Lint HTML') {
        steps {
	    sh 'tidy -q -e *.html'
	  }
	}
      stage('Security Scan') {
        steps {
	  aquaMicroscanner imageName: 'alpine:latest', notCompliesCmd: 'exit 1', onDisallowed: 'fail', outputFormat: 'html'
	  }
	}
      stage('AWS Credentials') {
        steps {
          withCredentials(
            [[
              $class: 'AmazonWebServicesCredentialsBinding',
              accessKeyVariable: 'AWS_ACCESS_KEY_ID',
              credentialsId: 'capstone',
              secretKeyVariable: 'AWS_SECRET_ACCESS_KEY'
              ]]) {
              sh """
                    mkdir -p ~/.aws
                    echo "[default]" > ~/.aws/credentials
                    echo "[default]" > ~/.boto
                    echo "aws_access_key_id = ${AWS_ACCESS_KEY_ID}" >> ~/.boto
                    echo "aws_secret_access_id = ${AWS_SECRET_ACCESS_KEY}" >> ~/.boto
                    echo "aws_access_key_id = ${AWS_ACCESS_KEY_ID}" >> ~/.aws/credentials
                    echo "aws_secret_access_id = ${AWS_SECRET_ACCESS_KEY}" >> ~/.aws/credentials
              """
              }
            }
      stage(‘Upload to AWS’) {
        steps {
          withAWS(region:’us-east-1’,credentials:’capstone’) {
            s3Upload(pathStyleAccessEnabled:true, payloadSigningEnabled: true, file:’index.html’, bucket:’c3pipelines’)
          }
        }
      }
    }
  }
}
