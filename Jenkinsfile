pipline {
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
        stage('Upload to AWS') {
            steps {
                withAWS(credentials:'aws-static') {
                    s3Upload(pathStyleAccessEnabled: true, payloadSigningEnabled: true, file:'index.html', bucket:'cybergoat-static')
                    sh 'echo "Hello World"'
                    sh '''
                        echo "Multiline shell steps works too"
                        ls -lah
                    '''
	        }   
            }
        }
    }
}
