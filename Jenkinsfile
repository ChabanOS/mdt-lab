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
		stage('Sonar') {
			steps{
				withSonarQubeEnv(installationName: 'sonarscanner4', credentialsId: 'student1-sonar') {
					scripts{
						sonarHome = tool 'sonarscanner4'
						sh """
						$(sonarHome)/bin/sonar-scanner -Dconar.projectKey=student1-project -Dsonar.sources=www -Dsonar.host.url=https://server1.jenkins-practice.tk
						"""
					}
				}
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
				cd ${WORKSPACE}/www
				tar --exclude='./css' --exclude='./js' -c -z -f ../site-archive-${params.RELEASE}-${params.RELEASE_VER}-${BUILD_NUMBER}.tgz .
				"""
			}
		}

		stage('Upload Artifact') {
			steps {
				nexusArtifactUploader artifacts: [
					[artifactId: 'site-archive-${RELEASE}-${RELEASE_VER}-${BUILD_NUMBER}', 
					classifier: '', 
					file: 'site-archive-${RELEASE}-${RELEASE_VER}-${BUILD_NUMBER}.tgz', 
					type: 'tgz']
				], 
				credentialsId: 'acd97e08-b27d-4677-a580-2d2555874441', 
				groupId: 'site', 
				nexusUrl: 'server2.jenkins-practice.tk', 
				nexusVersion: 'nexus3', 
				protocol: 'https', 
				repository: 'student1-repo', 
				version: '${RELEASE_VER}-${BUILD_NUMBER}'
			}
		}

	
  
}
}