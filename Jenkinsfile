pipeline {
    agent any
    
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
        
        stage ('Dependency-Check Analysis') {
            steps {
                sh 'echo "Dependency check"'
            }
        }
        
        stage ('Snyk Analysis') {
            steps {
		sh 'echo Snyk analysis'
            }
        }
                
        stage ('Deploy to App Server') {
            steps {
                    sh '''
                        echo "Deploying App to Server"
                        docker-compose up
                        '''

            }
        }
    }
}
