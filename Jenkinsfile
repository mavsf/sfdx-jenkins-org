#!groovy

node {

    def SF_CONSUMER_KEY=env.SF_CONSUMER_KEY
    def SF_USERNAME=env.SF_USERNAME
    def SERVER_KEY_CREDENTALS_ID=env.SERVER_KEY_CREDENTALS_ID
    def DEPLOYDIR='src'
    def TEST_LEVEL='RunLocalTests'
	
    SF_CONSUMER_KEY = env.CONNECTED_APP_CONSUMER_KEY
    SF_USERNAME = env.HUB_ORG
    SERVER_KEY_CREDENTALS_ID = env.JWT_KEY_CRED_ID
	println env.HUB_ORG
    println '===================================1' 
    println 'KEY IS'
    println 'SERVER_KEY_CREDENTALS_ID: ' + SERVER_KEY_CREDENTALS_ID
    println 'SF_USERNAME: '     	 + SF_USERNAME
    println 'SF_CONSUMER_KEY: ' 	 + SF_CONSUMER_KEY
    println '===================================2' 
    def toolbelt = tool 'toolbelt'
    println '===================================3' 

    // -------------------------------------------------------------------------
    // Check out code from source control.
    // -------------------------------------------------------------------------

    stage('checkout source') {
        checkout scm
    }
    println '===================================4' 

    // -------------------------------------------------------------------------
    // Run all the enclosed stages with access to the Salesforce
    // JWT key credentials.
    // -------------------------------------------------------------------------

    withCredentials([file(credentialsId: SERVER_KEY_CREDENTALS_ID, variable: 'server_key_file')]) {
        // -------------------------------------------------------------------------
        // Authenticate to Salesforce using the server key.
        // -------------------------------------------------------------------------

        stage('Authorize to Salesforce') {
            rc = command "${toolbelt}/sfdx force:auth:jwt:grant --instanceurl https://login.salesforce.com --clientid ${SF_CONSUMER_KEY} --jwtkeyfile ${server_key_file} --username ${SF_USERNAME} --setalias UAT"
            if (rc != 0) {
                error 'Salesforce org authorization failed.'
            }
        }


        // -------------------------------------------------------------------------
        // Deploy metadata and execute unit tests.
        // -------------------------------------------------------------------------

        stage('Deploy and Run Tests') {
            rc = command "${toolbelt}/sfdx force:mdapi:deploy --wait 10 --deploydir ${DEPLOYDIR} --targetusername UAT --testlevel ${TEST_LEVEL}"
            if (rc != 0) {
                error 'Salesforce deploy and test run failed.'
            }
        }


        // -------------------------------------------------------------------------
        // Example shows how to run a check-only deploy.
        // -------------------------------------------------------------------------

        //stage('Check Only Deploy') {
        //    rc = command "${toolbelt}/sfdx force:mdapi:deploy --checkonly --wait 10 --deploydir ${DEPLOYDIR} --targetusername UAT --testlevel ${TEST_LEVEL}"
        //    if (rc != 0) {
        //        error 'Salesforce deploy failed.'
        //    }
        //}
    }
}

def command(script) {
    if (isUnix()) {
        return sh(returnStatus: true, script: script);
    } else {
		return bat(returnStatus: true, script: script);
    }
}
