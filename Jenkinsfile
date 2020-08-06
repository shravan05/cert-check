#!/usr/bin/env groovy

/*

**Jenkins File for Scanning iD for all the Environments
**Author - Shravan Dasari
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


       ansiblePlaybook(
	     playbook: "${workspace}/ansible_scripts/certckeck.yml",
	     inventory: "${workspace}/ansible_scripts/inventory/${EnvType}",
	     colorizedOutput: true,
	     extras: "-e workspace=${workspace}")
     }

stage ('PrintResult'){

 if(!fileExists('results.txt')) {

	 sh 'echo Cert was not found ,results.txt doesnot exists'

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
  post {
      always {
          archiveArtifacts artifacts: '${WORKSPACE}/results.txt',
          onlyIfSuccessful: true
      }
  }
 }

def notifyBuild(String buildStatus = 'STARTED') {
// build status of null means successful
buildStatus =  buildStatus ?: 'SUCCESSFUL'


}
