pipeline {
    agent any


    stages {
        
        stage ('Initialization') {
            steps {
                sh 'echo "Starting the CI pipeline."'
            }
        }
        
        stage ('Check git secrets') {
            steps{
                sh '''
                    rm trufflehog.json || true
                    docker run gesellix/trufflehog --json https://github.com/v7nc3nz/dvja.git | tee trufflehog.json
                '''
            }
        }

        stage ('Build') {
            steps {
                sh '''
                    echo "Building Application"
                    docker-compose build 
                   '''
            }
        }
        
        stage ('SonarQube Analysis') {
            steps {
		        sh 'echo "SAST analysis"'
                }
            }    
        
        stage ('Retire.js Analysis') {
            steps {
                sh 'echo "check for retire js"'
            }
        }
        
        
        stage ('Snyk Analysis') {
            steps {
		        sh 'echo Snyk analysis'
                snykSecurity(
                snykInstallation: 'snyk',
                snykTokenId: 'SNYK_TOKEN',
                failOnIssues: false
        )
            }
        }
                
        stage ('Deploy to App Server') {
            steps {
                    sh '''
                        echo "Deploying App to Server"
                        docker-compose up -d
                    '''

            }
        }

        stage ('DAST - OWASP ZAP baseline') {
            steps {
                sh 'docker run -t owasp/zap2docker-stable zap-baseline.py -t http://128.199.21.116:8080/ || true'
            }
        }
    }
}
