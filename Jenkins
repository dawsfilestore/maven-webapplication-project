pipeline {
    agent any
    tools
    {
      maven "maven3.9.9"
    }
     stages
     {
        stage('git checkout')
        {
          steps
          {
             notifyBuild('STARTED')
             git branch: 'dev', url: 'https://github.com/dawsfilestore/maven-webapplication-project.git'
          }
        } //stage  close
        stage('build')
        {
        steps
        {
           sh "mvn clean package"
        }
        }  //stage close
        stage('SQ Report')
        {
           steps
           {
           sh "mvn sonar:sonar"
           }
        } //stage ending
        stage('Uploadinto Nexus')
        {
           steps
           {
              sh "mvn deploy"
           }
        }  //stage ending

        stage('tomcat deploy')
        {
        steps {
                sh """
            curl -u kk:password \
            --upload-file /var/lib/jenkins/workspace/declarative-PL-jio/target/maven-web-application.war \
            "http://15.207.153.48:8080/manager/text/deploy?path=/maven-web-application&update=true"
        """
            }
        }  //stage ending

     }  // stages closing



post {
  success {

    script
    {
     notifyBuild(currentBuild.result)
    }
    
  }
  failure {

  script
  {
    notifyBuild(currentBuild.result)

  }
   
  }
}


} // pipeline ending

// Notification method
def notifyBuild(String buildStatus = 'STARTED') {
    buildStatus = buildStatus ?: 'SUCCESS'

    def colorCode
    def subject = "${buildStatus}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'"
    def summary = "${subject} (${env.BUILD_URL})"

    switch (buildStatus) {
        case 'STARTED':
            colorCode = '#FFFF00' // Yellow
            break
        case 'SUCCESS':
            colorCode = '#00FF00' // Green
            break
        default:
            colorCode = '#FF0000' // Red
    }

    slackSend(color: colorCode, message: summary)
}
