pipeline {
    agent none
    options {
        skipStagesAfterUnstable()
    }
    stages {
        stage('Build') { 
            agent {
                docker {
		    label 'dind'
                    image 'python:2-alpine' 
                }
            }
            steps {
                sh 'python -m py_compile sources/add2vals.py sources/calc.py' 
            }
        }
        
        stage('Test') {
            agent {
                docker {
		    label 'dind'
                    image 'qnib/pytest'
                }
            }
            steps {
                sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
            }
            post {
                always {
                    junit 'test-reports/results.xml'
                }
            }
        }
        
        stage('Deliver') {
            agent {
                docker {
		    label 'dind'
                    image 'cdrx/pyinstaller-linux:python2'
                }
            }
            steps {
                sh 'pyinstaller --onefile sources/add2vals.py'
            }
            post {
                success {
                    echo "Deploy CF stage success"
        	        mail to:"steve.ho@ge.com", subject:"success: ${currentBuild.fullDisplayName}", body: "Cloud Foundry Deployment success"
                    archiveArtifacts 'dist/add2vals'
                }
                failure {
					echo "Deploy CF stage failed"
        	        mail to:"steve.ho@ge.com", subject:"FAILURE: ${currentBuild.fullDisplayName}", body: "Cloud Foundry Deployment failed"
				}
            }
        }
    }
}

 
