pipeline {
	    agent any 	
		environment {
		PROJECT_ID = 'clean-mason-262222'
		CLUSTER_NAME = ' sprint6-kubectl-cluster'
		LOCATION = 'us-central1-c'
		CREDENTIALS_ID = 'sprint6-k8' 
		}
		
	    stages {	
		   stage('Scm Checkout') {            
			steps {
	                  checkout scm
			}	
	           }
	           
		   stage('Build') { 
	                steps {
	                  echo "Cleaning and packaging..."
	                  sh 'mvn clean package'		
	                }
	           }
		   stage('Test') { 
			steps {
		          echo "Testing..."
			  sh 'mvn test'
			}
		   }
		   stage('Build Docker Image') { 
			steps {
	                   script {
	                      myapp = docker.build("loveythakral/sprint6kube8:${env.BUILD_ID}")
	                   }
	                }
		   }
		   stage("Push Docker Image") {
	                steps {
	                   script {
			      docker.withRegistry('https://registry.hub.docker.com', 'docker-hub') {
	                            myapp.push()		
	                     }
				   
	                   }
	                }
	            }
		   
	           stage('Deploy to K8s') { 
	                steps{
	                   echo "Deployment started ..."
			   sh 'ls -ltr'
			   sh 'pwd'
			   sh "sed -i 's/tagversion/${env.BUILD_ID}/g' deployment.yaml"
	                  // step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID, clusterName: env.CLUSTER_NAME, location: env.LOCATION, manifestPattern: 'deployment.yaml', credentialsId: env.CREDENTIALS_ID, verifyDeployments: true])
			     step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID, clusterName: env.CLUSTER_NAME, location: env.LOCATION, manifestPattern: 'deployment.yaml', credentialsId: env.CREDENTIALS_ID, verifyDeployments: true])
				
			   echo "Deployment Finished ..."
	            }
	          }
	    }
	}
