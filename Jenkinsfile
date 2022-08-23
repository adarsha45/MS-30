node{
    def SF_CONSUMER_KEY=env.SF_CONSUMER_KEY
    def SF_USERNAME=env.SF_USERNAME
    def SERVER_KEY_CREDENTALS_ID=env.SERVER_KEY_CREDENTALS_ID
    def SF_DEV_HUB_ALIAS=env.SF_DEV_HUB_ALIAS
    def SF_SCRATCH_ALIAS=env.SF_SCRATCH_ALIAS
    def SF_INSTANCE_URL = env.SF_INSTANCE_URL ?: "https://login.salesforce.com"
    def PKG_PATH = "sfdx-source/force-app"
    def Workspace = pwd()
    
    println 'Key is'
    println SF_CONSUMER_KEY
    println SERVER_KEY_CREDENTALS_ID
    println SF_USERNAME
    println SF_DEV_HUB_ALIAS
    println SF_INSTANCE_URL

    stage('checkout source') {
        checkout scm
        echo 'this is adarsha'
        sh "${PKG_PATH}/main/default/ada.sh"
    }
    withCredentials([file(credentialsId: SERVER_KEY_CREDENTALS_ID, variable: 'server_key_file')]) {
        stage('Authorize DevHub') {
             rc = command "sfdx force:auth:jwt:grant --instanceurl ${SF_INSTANCE_URL} --clientid ${SF_CONSUMER_KEY} --username ${SF_USERNAME} --jwtkeyfile ${server_key_file} --setdefaultdevhubusername --setalias ${SF_DEV_HUB_ALIAS}"
             if (rc != 0) {
                 error 'Salesforce dev hub org authorization failed.'
             }
         }
        }

    stage('Verify Featurebranch'){
        properties([
            parameters([
                string(name:'Featurebranchname',defaultValue:'Feature/MS-1.0.0', description:'Enter Feature Branch Name'),
            ])
        ])
    }

    
    stage("Validate Main Package"){
        try{
            sh "sfdx force:source:deploy -p ${PKG_PATH} --targetusername ${SF_USERNAME} --testlevel RunLocalTests -c"
                //"sfdx force:source:deploy -p ${PACKAGE_PATH} -l RunLocalTests -w 100 -u ${VERIFICATION_ORG_ALIAS} --verbose" 
                //sh "sfdx force:source:deploy -p force-app -w 100 -u ${VERIFICATION_ORG_ALIAS}"
                //sh "sfdx force:mdapi:deploy -d externalPackage -w 100 -u ${VERIFICATION_ORG_ALIAS}"
            }catch(exception){
                // Since we are using try..catch block have to fail the build manually
                currentBuild.result = "FAILED"
                throw exception
            }
        }
}