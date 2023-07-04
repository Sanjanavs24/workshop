import groovy.json.JsonSlurper

def getFtpPublishProfile(def publishProfilesJson) {
  def pubProfiles = new JsonSlurper().parseText(publishProfilesJson)
  for (p in pubProfiles)
    if (p['publishMethod'] == 'FTP')
      return [url: p.publishUrl, username: p.userName, password: p.userPWD]
}

node {
  withEnv(['AZURE_SUBSCRIPTION_ID=2092c611-ff69-417d-b89e-3083ef2771e8',
        'AZURE_TENANT_ID=5f3426ec-85cf-46fb-a173-65769088cd2e']) {
    stage('init') {
      checkout scm
    }
  
    stage('build') {
      sh 'mvn clean package'
    }
  
    stage('deploy') {
      def resourceGroup = 'workshop24_group'
      def webAppName = 'workshop24'
      // login Azure
      withCredentials([usernamePassword(credentialsId: 'workshop', passwordVariable: '63y8Q~L1MKHBUZ9u2dOwFfNF4-Ncti-KlzvcfcMz', usernameVariable: 'de8134c7-c831-45e7-a28e-49e0fa8a0068')]) {
       sh '''
          az login --service-principal -u $de8134c7-c831-45e7-a28e-49e0fa8a0068 -p $63y8Q~L1MKHBUZ9u2dOwFfNF4-Ncti-KlzvcfcMz -t $5f3426ec-85cf-46fb-a173-65769088cd2e
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
