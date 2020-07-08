pipeline {
	agent {
		label 'Demo'
	}
   
	parameters {
		string(defaultValue: '0.0.1', description: '', name: 'RELEASE_VER', trim: false)
		choice(choices: ['DEVELOP', 'RELEASE'], description: '', name: 'RELEASE')
	}
	tools {
		nodejs 'Node12'
	}

	stages {
		stage('Checkout') {
			steps {
				git 'https://github.com/ChabanOS/mdt-lab'
			}
		}
		stage('Build') {
		parallel {
            stage('JS') {
                steps {
                    sh label: 'minimize JS', script: """
                    cd ${WORKSPACE}/www/js
                    uglifyjs --timings init.js -o ../min/custom-min.js
                    """
                }
            }
            stage('CSS') {
                steps {
                    sh label: 'minimize CSS', script: """
                    cd ${WORKSPACE}/www/css
                    cleancss -d style.css > ../min/custom-min.css
					"""
                }
            }
		}
	}
		stage('Archie mdt') {
			steps {
				sh label: 'Archive artefact', script: """
				cd ${WORKSPACE}
				tar --exclude='./css' --exclude='./js' -c -z -f ../site-archive-${params.RELEASE}-${params.RELEASE_VER}-${BUILD_NUMBER}.tgz .
				"""
			}
		}

		stage('Create Artifact') {
			steps {
				archiveArtifacts artifacts: '*.tar'
			}
		}

	
  
}
}