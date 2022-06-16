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
                    echo building
                   '''
            }
        }
        
        stage ('SonarQube Analysis') {
            environment {
                scannerHome = tool 'SonarQube Scanner'
            }
            steps {
		sh 'SAST analysis'
                }
            }    
        }
        
        stage ('Retire.js Analysis') {
            steps {
                sh 'retire --path `pwd` --outputformat json --outputpath /var/lib/jenkins/reports/retirejs-report --exitwith 0'
            }
        }
        
        stage ('Dependency-Check Analysis') {
            steps {
                sh '/var/lib/jenkins/dependency-check/bin/dependency-check.sh --scan `pwd` --format JSON --out /var/lib/jenkins/reports/dependency-check-report --prettyPrint'
            }
        }
        
        stage ('Snyk Analysis') {
            steps {
                sh '/home/chaos/snyk.sh'
            }
        }
                
        stage ('Deploy to App Server') {
            steps {
                    sh 'echo "Deploying App to Server"'
            }
        }
    }
}
