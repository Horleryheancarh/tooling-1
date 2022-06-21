pipeline {
    agent any

	environment {
		DOCKERHUB_CREDENTIALS=credentials('dockerhub')
	}
	
	stages {
		stage("Initial cleanup") {
			steps {
				dir("${WORKSPACE}") {
					deleteDir()
				}
			}
		}

    	stage('Clone Github Repo') {
      		steps {
            	git branch: 'main', url: 'https://github.com/Horleryheancarh/toooling-1.git'
      		}
    	}

		stage ('Build Docker Image') {
			steps {
				script {
					sh 'docker build -t yheancarh/php_tooling-1:${BRANCH_NAME}-${BUILD_NUMBER} .'
				}
			}
		}
		
		stage ('Run docker compose') {
			steps {
				script {
					sh 'docker-compose -f tooling.yaml up -d'
				}
			}
		}
	
		stage ('Test Endpoint') {
			steps {
				script {
					while (true) {
						def res = httpRequest 'http://localhost:5000'
					}
				}
			}
		}

		stage ('Push Image To Docker Hub') {
			when { expression { res.status == 200 } }
			steps {
				script {
					sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'

					sh 'docker push yheancarh/php_tooling-1:${BRANCH_NAME}-${BUILD_NUMBER}'
				}
			}
		}

		stage('Cleanup') {
			steps {
				sh 'docker-compose -f tooling.yaml down'

				sh 'docker logout'

				sh 'docker system prune -f'

				cleanWs(cleanWhenAborted: true, cleanWhenFailure: true, cleanWhenNotBuilt: true, cleanWhenUnstable: true, deleteDirs: true)
			}
		}
  	}
}