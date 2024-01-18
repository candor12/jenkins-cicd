pipeline {
	options {
		buildDiscarder(logRotator(numToKeepStr: '8'))
                skipDefaultCheckout() 
                disableConcurrentBuilds() 
		ansiColor('xterm')
	}
	agent any
	environment {
		branch           =       "docker-multiservices"
		repoUrl          =       "https://github.com/candor12/cicd_jenkins.git"
		gitCreds         =       "gitPAT"
	        scannerHome      =       tool 'sonar4.7'
	        ecrRepo          =       "674583976178.dkr.ecr.us-east-2.amazonaws.com/teamimagerepo"
	        dockerImage      =       "${env.ecrRepo}:${env.BUILD_ID}" 
	}
	stages{
		stage('SCM Checkout') {
			steps {
				git branch: branch, url: repoUrl, credentialsId: 'gitPAT'
			}
		}
		stage('Docker Image Build') {
			agent { label 'agent1' }
			steps {
				script { 
					cleanWs()
					git branch: branch, url: repoUrl
					sh "docker-compose build"
					sh "docker-compose images"
				}
			}
		}
		
	/*	stage('Push Image to ECR') {
			agent { label 'agent1' }
			steps {
				script {
					def status = sh(returnStatus: true, script: 'docker push $dockerImage')
					if (status != 0) {
					    sh "aws ecr get-authorization-token --region us-east-2 --output text --query 'authorizationData[].authorizationToken' | base64 -d | cut -d: -f2 > ecr.txt"
                                            sh 'cat ecr.txt | docker login -u AWS 674583976178.dkr.ecr.us-east-2.amazonaws.com --password-stdin'
					    sh 'docker push $dockerImage'
					}
					sh "docker push ${ecrRepo}:latest"
				}
			}
			post { 
				always {
					sh """ rm -f ecr.txt
					docker rmi -f ${dockerImage}
					docker rmi -f ${ecrRepo}:latest
				        """ 
				}
			}
		}
		stage('EKS Deployment') {
			agent { label 'agent1' }
			when { expression { return params.EksDeploy } }
			steps {
				script { 
					dir('k8s') {
						sh "chmod +x ./cluster.sh && ./cluster.sh" 
						sh '''kubectl apply -f ./eksdeploy.yml
                                                kubectl get deployments && sleep 5 && kubectl get svc
				                '''   
					}
				}
			}
		} */
	} 
	post { always { cleanWs() } }
}
