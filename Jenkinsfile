#!/usr/bin/env groovy

/*

**Jenkins File for Scanning iD for all the Environments
**Author - Anudeep Seri
**Version 1.0
*/

node {

deleteDir()


stage('git checkout') {

checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [],
gitTool: 'GIT-Linux', submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'd5cba97a-d1e4-420f-bbd1-612e889a15b2',
url: 'https://github.com/shravan05/cert-check']]])

}

stage ('playbook run') {
timestamps {
	try{

	//notifyBuild('STARTED')

  ansiColor('xterm') {
	ansiblePlaybook(
		     playbook: "${workspace}/ansible_scripts/certckeck.yml",
		     inventory: "${workspace}/ansible_scripts/inventory/${EnvType}",
		     colorized: true,
		     extras: "-e workspace=${workspace}")
	 }
stage ('PrintResult'){

 if(!fileExists('results.txt')) {

	 sh 'echo result was not found ,results.txt doesnot exists'

	 }
 else {

    sh 'cat ${WORKSPACE}/results.txt'
}
}
 }catch (e) {
  	    currentBuild.result = "FAILED"
  	    throw e
  	    } finally {
  	    notifyBuild(currentBuild.result)
  	}
  }
 }
}

def notifyBuild(String buildStatus = 'STARTED') {
// build status of null means successful
buildStatus =  buildStatus ?: 'SUCCESSFUL'

emailext(
attachLog: true,
attachmentsPattern: 'results.txt',
to: '${EmailAddress}',
subject: "${buildStatus} : Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
mimeType: 'text/html',
body: "Check Console output at $BUILD_URL to view the logs.",
recipientProviders: [[$class: 'DevelopersRecipientProvider']]
)
}
