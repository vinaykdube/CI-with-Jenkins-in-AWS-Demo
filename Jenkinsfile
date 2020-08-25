pipeline {
        agent any
        environment {
            registry = "vinaykdube/sprint6dockerjenkinsgcloud"
            registryCredential = 'dockerhubvinay'
            dockerImage = ''
		PROJECT_ID = 'cobalt-alcove-279108'
 		CLUSTER_NAME = 'kube-cluster-gcloud1'
 		LOCATION = 'us-central1-c'
 		CREDENTIALS_ID = 'k8-jenkins'
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
		          echo "Testing...."
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
