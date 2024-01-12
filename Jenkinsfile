import groovy.json.JsonSlurper

def getFtpPublishProfile(def publishProfilesJson) {
  def pubProfiles = new JsonSlurper().parseText(publishProfilesJson)
  for (p in pubProfiles)
    if (p['publishMethod'] == 'FTP')
      return [url: p.publishUrl, username: p.userName, password: p.userPWD]
}

node {
  withEnv(['AZURE_SUBSCRIPTION_ID=90e29109-e9c2-4b40-88a6-7fe6f3593db9>',
        'AZURE_TENANT_ID=511ce6e0-0e57-4c9f-acd1-4154a6b4c914']) {
    stage('init') {
      checkout scm
    }
  
    stage('build') {
      sh 'mvn clean package'
    }
  
    stage('deploy') {
      def resourceGroup = 'RG-AzureChallenge'
      def webAppName = 'PipelinesTest'
      // login Azure
      withCredentials([usernamePassword(credentialsId: '<service_princial>', passwordVariable: 'AZURE_CLIENT_SECRET', usernameVariable: 'AZURE_CLIENT_ID')]) {
       sh '''
          az login --service-principal -u $8949e385-02e1-447b-8070-0191a3360ac0 -p $CsI8Q~Ov1BM2~XOxSAqMJoYKh7K0s54VJGSCace1 -t $511ce6e0-0e57-4c9f-acd1-4154a6b4c914
          az account set -s $90e29109-e9c2-4b40-88a6-7fe6f3593db9
        '''
      }
      // get publish settings
      def pubProfilesJson = sh script: "az webapp deployment list-publishing-profiles -g $resourceGroup -n $webAppName", returnStdout: true
      def ftpProfile = getFtpPublishProfile pubProfilesJson
      // upload package
      sh "curl -T target/calculator-1.0.war $ftpProfile.url/webapps/ROOT.war -u '$ftpProfile.username:$ftpProfile.password'"
      // log out
      sh 'az logout'
    }
  }
}
