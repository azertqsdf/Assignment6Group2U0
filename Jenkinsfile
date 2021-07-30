def commit_id
pipeline {
	agent any
	stages{
		stage('preparation'){
			steps{  checkout scm
				sh "git rev-parse --short HEAD > .git/commit-id"
				script {
				commit_id = readFile ('.git/commit-id').trim()
					}
				}	
			}
		stage('build'){
			steps{ echo '1. BUILDING MAVEN WOKRLOAD'
				sh "mvn clean install"
				echo 'Build completed' }	
	 		}
		
		stage('image build'){
			steps{ echo '2. BUILDING DOCKER image'
				sh "docker build -t position-simulator:${commit_id} ."
				echo 'Build completed'}	
			}
stage ("image push") {
            steps {
                echo 'pushing docker image'
                sh "docker push 2alinfo7/position-simulator:${commit_id}"
                echo 'docker image pushed'
            }
        }
		stage('deploy'){
			steps{ echo '3. DEPLOYMENT to kubernetes'
				sh "sed -i -r 's|richardchesterwood/k8s-fleetman-webapp-angular:release2|position-simulator:${commit_id}|' workloads.yaml"
				sh "kubectl apply -f workloads.yaml "
				echo "deployment completed"	
			}
	}
	}
} 

