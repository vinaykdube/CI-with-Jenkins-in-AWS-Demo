pipeline {
        agent any
        environment {
            registry = "loveythakral/sprint6dockerjenkinsgcloud"
            registryCredential = 'dockerhubcredentials'
            dockerImage = ''
		PROJECT_ID = 'devops-super10x'
 		CLUSTER_NAME = 'sprint6-kubectl-cluster-gcloud'
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
	                      dockerImage = docker.build registry + ":$BUILD_NUMBER"
	                   }
	                }
		   }
            stage('Deploy Image') {
                steps{
                    script {
                    docker.withRegistry( '', registryCredential ) {
                        dockerImage.push()
                    }
                    }
                }
            }
		 stage('Create cluster') {
 			steps{
 				echo "Cluster create started"
				 sh 'gcloud config set project devops-super10x'
				sh 'gcloud config set compute/zone us-central1-c'
				sh 'gcloud container clusters create sprint6-kubectl-cluster-gcloud --num-nodes=1'
				sh 'gcloud container clusters get-credentials sprint6-kubectl-cluster-gcloud'
 				echo "Cluster create completed"
 	            }
	          }

	           stage('Deploy to GKE') {
 			steps{
 				echo "Deployment started"
				sh 'ls -ltr'
				sh 'pwd'
				sh "sed -i 's/tagversion/${env.BUILD_ID}/g' deployment.yaml"
				step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID,
				      clusterName: env.CLUSTER_NAME, location: env.LOCATION, manifestPattern: 'deployment.yaml',
				      credentialsId: env.CREDENTIALS_ID, verifyDeployments: true])
				echo "Deployment Finished"
 	            }
	          }
	    }
	}
