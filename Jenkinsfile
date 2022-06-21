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
                    docker container ls -a | grep gesellix/trufflehog | awk -F" " '{print $1}' | xargs docker container rm || true
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
                withSonarQubeEnv('sonar') {
			        sh 'mvn sonar:sonar'
			        sh 'cat target/sonar/report-task.txt'
		       }
                }
            }

        stage ('Retire.js Analysis') {
            steps {
                sh 'retire --outputformat json | tee retire.json'
            }
        }    
        
        stage ('Snyk Analysis') {
            steps {
		        sh 'echo Snyk analysis'
                snykSecurity(snykInstallation: 'snyk', snykTokenId: 'SNYK_TOKEN', failOnIssues: false)
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

        stage('DAST ') {
            parallel {
            stage ('DAST - OWASP ZAP baseline') {
                steps {
                    sh '''
                    sleep 20
                    docker run -t owasp/zap2docker-stable zap-baseline.py -t http://128.199.21.116:8888/ || true
                    docker container ls -a | grep zap2docker | awk -F" " '{print $1}' | xargs docker container rm || true
                    '''
                    }
                }

            stage ('Nikto Scan') {
		        steps {
			        sh 'rm nikto-output.xml || true'
			        sh 'docker pull secfigo/nikto:latest'
			        sh 'docker run --user $(id -u):$(id -g) --rm -v $(pwd):/report -i secfigo/nikto:latest -h 54.86.226.84 -p 8080 -output /report/nikto-output.xml'
			        sh 'cat nikto-output.xml'   
		        }
	        }
	    
	        stage ('SSL Checks') {
		        steps {
			        sh 'pip install sslyze==1.4.2'
			        sh 'python -m sslyze --regular 128.199.21.116:8888 --json_out sslyze-output.json'
			        sh 'cat sslyze-output.json'
		            }
	            }
            }

        stage ('Send reports to DefectDojo') {
            steps {
                sh '''
                    echo "Reports sent"
                '''
            }
        }
    }
}
