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
    				withCredentials([string(credentialsId: 'password_dockerhub', variable: 'DOCKER_HUB_PASSWORD')]) {
      					sh 'sudo docker login -u bafof -p $DOCKER_HUB_PASSWORD'
      					sh 'printenv'
      					sh 'sudo docker build -t bafof/devops-app:""$GIT_COMMIT"" .'
      					sh 'sudo docker push bafof/devops-app:""$GIT_COMMIT""'
    				}

  			}
		}
		//--------------------------
		stage('Deployment Kubernetes  ') {
  			steps {
    				withKubeConfig([credentialsId: 'idbfkubernetes']) {
           				sh "sed -i 's#replace#bafof/devops-app:${GIT_COMMIT}#g' k8s_deployment_service.yaml"
           				sh "kubectl apply -f k8s_deployment_service.yaml"
         			}
  			}
		}

	}
}
