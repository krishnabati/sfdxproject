#!groovy
import groovy.json.JsonSlurperClassic
node {

    def BUILD_NUMBER=env.BUILD_NUMBER
    def RUN_ARTIFACT_DIR="tests/${BUILD_NUMBER}"
    def SFDC_USERNAME

    def HUB_ORG=env.HUB_ORG_DH
    def SFDC_HOST = env.SFDC_HOST_DH
    def JWT_KEY_CRED_ID = env.JWT_CRED_ID_DH
    def CONNECTED_APP_CONSUMER_KEY=env.CONNECTED_APP_CONSUMER_KEY_DH

    println 'KEY IS' 
    println JWT_KEY_CRED_ID
    println HUB_ORG
    println SFDC_HOST
    println CONNECTED_APP_CONSUMER_KEY
        
    stage('checkout source') {
        // when running in multi-branch job, one must issue this command
        checkout scm
    }
    // extra code added
    // server_key_files="/var/lib/jenkins/workspace/newjob_/certs/server.key"
    // echo server_key_files
    
    //  SF_AUTH_URL = env.SFDX_AUTH_URL
    // echo SF_AUTH_URL
    // writeFile file: 'authjenkinsci.txt', text: SF_AUTH_URL
    // sh 'ls -l authjenkinsci.txt'
    // sh 'cat authjenkinsci.txt'
    // echo 'end sf auth method'

    // extra code ended here
    withCredentials([file(credentialsId: JWT_KEY_CRED_ID, variable: 'jwt_key_file')]) {
        stage('Deploye Code') {
            if (isUnix()) {
                
                //  sh 'cd /var/lib/jenkins/workspace/newjob1_'
                // rc = sh returnStatus: true, script: "sfdx force:auth:sfdxurl:store -f authjenkinsci.txt -a vscodeaws"
               rc = sh returnStatus: true, script: '''
export SFDX_USE_GENERIC_UNIX_KEYCHAIN=true
echo Above Set Value: $SFDX_USE_GENERIC_KEYCHAIN
echo Shell is: $SHELL
which secret-tool
which sfdx
sfdx force:auth:jwt:grant --clientid 3MVG9fe4g9fhX0E6cxIQNLr5l73TAm3m_yZ1L3WckxNkkpyQU0__JmpryOXnunFwBGmd_nIhYbE5EUUd0RfRN --username sfdxdevelopment2020@gmail.com --jwtkeyfile 0402b8e2-c9ff-4b9b-bd2b-25bb0aa36012 -a vscodeaws --instanceurl https://login.salesforce.com'''

               // rc = sh returnStatus: true, script: '''sfdx force:auth:jwt:grant --clientid ${CONNECTED_APP_CONSUMER_KEY} --username ${HUB_ORG} --jwtkeyfile ${JWT_KEY_CRED_ID} -a vscodeaws --instanceurl ${SFDC_HOST}'''
            }else{
                 rc = bat returnStatus: true, script: "sfdx force:auth:jwt:grant --clientid ${CONNECTED_APP_CONSUMER_KEY} --username ${HUB_ORG} --jwtkeyfile \"${JWT_KEY_CRED_ID}\" --setdefaultdevhubusername --instanceurl ${SFDC_HOST}"
            }
            if (rc != 0) { error 'hub org authorization failed' }

			println rc
			
			// need to pull out assigned username
			if (isUnix()) {
				rmsg = sh returnStdout: true, script: "sfdx force:mdapi:deploy -d manifest/. -u ${HUB_ORG}"
			}else{
			   rmsg = bat returnStdout: true, script: "sfdx force:mdapi:deploy -d manifest/. -u ${HUB_ORG}"
			}
			  
            printf rmsg
            println('Hello from a Job DSL script!')
            println(rmsg)
        }
    }
}
