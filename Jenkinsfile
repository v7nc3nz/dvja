pipeline {
    agent any
    
    environment {
        SNYK_TOKEN = credentials('SNYK_TOKEN')
    }

    stages {
        
        stage ('Initialization') {
            steps {
                sh 'echo "Starting the CI pipeline."'
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
                sh '''
                ./snyk test --json | snyk-to-html -o results.html
                cp results.html /root/workspace/DevSecOps/report
                '''
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
                sh 'docker run -t owasp/zap2docker-stable zap-baseline.py -t http://128.199.21.116:8080/'
            }
        }
    }
}
