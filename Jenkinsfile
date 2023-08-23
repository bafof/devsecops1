pipeline {
	agent any
	stages {
      		stage('Build Artifact') {
            		steps {
              			sh "mvn clean package -DskipTests=true"
              			archive 'target/*.jar'  //test
            		}
        	}   
 		stage('UNIT test & jacoco') {
			steps {
				sh "mvn test"
			}
			post {
				always {
					junit 'target/surefire-reports/*.xml'
					jacoco execPattern: 'target/jacoco.exec'
				}
			}
		}
		//--------------------------
		stage('Docker Build and Push') {
  			steps {
    				withCredentials([string(credentialsId: 'adceee31-b1de-4411-87af-ad56ab93e129', variable: 'DOCKER_HUB_PASSWORD')]) {
      					sh 'sudo docker login -u barif -p $DOCKER_HUB_PASSWORD'
      					sh 'printenv'
      					sh 'sudo docker build -t barif/devops-app:""$GIT_COMMIT"" .'
      					sh 'sudo docker push barif/devops-app:""$GIT_COMMIT""'
    				}

  			}
		}
		//--------------------------
		stage('Deployment Kubernetes  ') {
  			steps {
    				withKubeConfig([credentialsId: 'kubeconfig']) {
           				sh "sed -i 's#replace#barif/devops-app:${GIT_COMMIT}#g' k8s_deployment_service.yaml"
           				sh "kubectl apply -f k8s_deployment_service.yaml"
         			}
  			}
		}

	}
}
