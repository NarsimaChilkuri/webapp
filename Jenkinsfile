pipeline{
	agent any 
	environment{
		WEB_APP1_TAG = getWebApp1DockerTag()
                WEB_APP2_TAG = getWebApp2DockerTag() 
	}
	stages{
		            stage('Build docker image'){
			               steps{
				                     sh "docker build . -t 74744556/webapp1:${WEB_APP1_TAG}"
                             sh "docker build . -t 74744556/webapp2:${WEB_APP2_TAG}"
			               }
		            }
		            stage('DockerHub Push'){
			               steps{
                                withCredentials([usernamePassword(credentialsId: 'dockerhub1', passwordVariable: 'dockerpass', usernameVariable: 'dockeruser')]) {
                                      sh "docker login -u ${dockeruser} -p ${dockerpass}"
                                      sh "docker push ${dockeruser}/webapp1:${WEB_APP1_TAG}"
                                      sh "docker push ${dockeruser}/webapp1:${WEB_APP2_TAG}"
                                }
			               }
		            }           



                stage('Deploy on kubernetes DEV env'){
                     environment{
                                  STACK = "dev"  
                     }
                     steps{     
                            deployHelmChart(STACK)
                     }
                }
                
                
                stage('Manual approval for UAT'){
                      steps{
                             input "Deploy to UAT?"
                        }
                }

		            stage('Deploy on kubernetes UAT env'){
		                 environment{
				                          STACK = "uat"	 
			               }
			               steps{     
                            deployHelmChart(STACK)
			               }
		            }
                
                stage('Manual approval for PROD'){
                      steps{
                             input "Deploy to PROD?"
                        }
                }
                stage('Deploy on kubernetes PROD env'){
                      environment{
                                   STACK = "prod"
                      }
                      steps{  
                             deployHelmChart(STACK)  
                      }
                }


	}

}

def getWebApp1DockerTag(){
	def tag = sh script: "git rev-parse webapp-1", returnStdout: true
	return tag
}

def getWebApp2DockerTag(){
        def tag = sh script: "git rev-parse webapp-2", returnStdout: true
        return tag
}

def deployHelmChart(String env){

   withCredentials([usernamePassword(credentialsId: 'kubehost', passwordVariable: 'pass', usernameVariable: 'userName')]) {
                            
                                  sh "ssh vagrant@172.42.42.100 rm -rf helm-charts"
                                  sh "ssh vagrant@172.42.42.100 mkdir helm-charts"
                                  sh "scp -o StrictHostKeyChecking=no -rv /home/narsimac/jenkins/webapps/webapp-chart  vagrant@172.42.42.100:/home/vagrant/helm-charts/webapp-chart"
                                  sh "ssh vagrant@172.42.42.100 'helm upgrade --install webapp helm-charts/webapp-chart/. -f helm-charts/webapp-chart/values-${env}.yaml -n ${env} --set=webapp1.tag=${WEB_APP1_TAG} --set=webapp2.tag=${WEB_APP2_TAG}'"
   }

}
