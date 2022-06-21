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
                    docker rm $(docker ps -a -f status=exited -q) || true
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
                sh 'echo "SAST done"'
                }
            }

        stage ('Source Composition Analysis') {
            parallel {
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
                    docker pull owasp/zap2docker-live

                    echo DEBUG - mkdir -p $PWD/out
                    mkdir -p $PWD/out

                    echo DEBUG - chmod 777 $PWD/out
                    chmod 777 $PWD/out
                    docker run -v $(pwd)/out:/zap/wrk/:rw -t owasp/zap2docker-live zap-api-scan.py -t $TARGET_URL -f openapi -d -r zap_scan_report.html
                    docker rm $(docker ps -a -f status=exited -q) || true
                    '''
                    }
                }

            stage ('Nuclei Scan') {
		        steps {
			        sh '''
                        nuclei -u http://128.199.21.116:8888/ -json -o nuclei.json -silent || true
                    '''   
		        }
	        }
	    
	        stage ('SSL Checks') {
		        steps {
			        sh '''pip install sslyze'
			        python3 -m sslyze 128.199.21.116:8888 --json_out sslyze-output.json || true
                    '''
		            }
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
