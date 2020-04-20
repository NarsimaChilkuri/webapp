pipeline{
	agent any 
	environment{
		            WEB_APP1_TAG = getWebApp1DockerTag()
	}
	stages{
		            stage('Build docker image'){
			               steps{
				                      sh "docker build . -t 74744556/webapp1:${WEB_APP1_TAG}"
			               }
		            }
		            stage('DockerHub Push'){
			               steps{
                                withCredentials([usernamePassword(credentialsId: 'dockerhub1', passwordVariable: 'dockerpass', usernameVariable: 'dockeruser')]) {
                                      sh "docker login -u ${dockeruser} -p ${dockerpass}"
                                      sh "docker push ${dockeruser}/webapp1:${WEB_APP1_TAG}"
                                }
			               }
		            }           



        }


	}



def getWebApp1DockerTag(){
    def tag = sh script: "git rev-parse webapp-1", returnStdout: true 
    return tag
}

