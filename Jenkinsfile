pipeline {
     agent { label 'Jenkins-Agent' }
     tools {
       jdk 'java17'
       maven 'Maven3'
     }
  stages{
     stages("Cleanup Workspace"){
              steps {
                cleanWs()
              }
     }
stage("Checkout from SCM"){
        steps {
          git branch: 'main', credentialsID: 'github', url: 'https://github.com/SaideepRoyal/register-app'
        }
}

  stage("Build Application"){
      steps {
            sh "mvn clean package"
      }
  }

  stage("Test Application"){
        steps {
          sh "mvn test"
        }
     }
 }
}
