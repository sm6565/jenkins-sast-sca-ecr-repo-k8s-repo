pipeline {
  agent any
  tools { 
        maven 'Maven_3_6_3'  
    }
   stages{
    stage('CompileandRunSAST') {
            steps {	
		sh 'mvn clean verify sonar:sonar -Dsonar.projectKey=smdemo -Dsonar.organization=setth -Dsonar.host.url=https://sonarcloud.io -Dsonar.login=f6fc24f42103facc3402fdd5a39b0ccec8ea0444'
			}
    }

	stage('RunSCA') {
            steps {		
				withCredentials([string(credentialsId: 'SNYK_TOKEN', variable: 'SNYK_TOKEN')]) {
					sh 'mvn snyk:test -fn'
				}
			}
    }

	stage('Build') { 
            steps { 
               withDockerRegistry([credentialsId: "dockerlogin", url: ""]) {
                 script{
                 app =  docker.build("webapp1")
                 }
               }
            }
    }

	stage('Push') {
            steps {
                script{
                    docker.withRegistry('https://572392880480.dkr.ecr.us-east-1.amazonaws.com/webapp1', 'ecr:us-east-1:aws-credentials') {
                    app.push("latest")
                    }
                }
            }
    	}
	   
	stage('Kubernetes Deployment Web Application') {
	   steps {
	      withKubeConfig([credentialsId: 'kubelogin']) {
		  sh '''curl -LO "https://s3.us-west-2.amazonaws.com/amazon-eks/1.21.14/2022-10-31/bin/linux/amd64/kubectl"
                        chmod +x kubectl       
		        ./kubectl get pods'''
		}
	      }
   	}

  }
}
