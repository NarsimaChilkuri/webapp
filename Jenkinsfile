pipeline{
	agent any 
	environment{
		            WEB_APP2_TAG = getWebApp2DockerTag()
	}
	stages{
		            stage('Build docker image'){
			               steps{
				                      sh "docker build . -t 74744556/webapp2:${WEB_APP2_TAG}"
			               }
		            }
		            stage('DockerHub Push'){
			               steps{
                                withCredentials([usernamePassword(credentialsId: 'dockerhub1', passwordVariable: 'dockerpass', usernameVariable: 'dockeruser')]) {
                                                 sh "docker login -u ${dockeruser} -p ${dockerpass}"
                                                 sh "docker push ${dockeruser}/webapp2:${WEB_APP2_TAG}"
                                           }
			               }
		            }           



        }

}



def getWebApp2DockerTag(){
    def tag = sh script: "git rev-parse webapp-2", returnStdout: true 
    return tag
}
