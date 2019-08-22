pipeline {
  agent any 
  stages {
    stage('Build') {
           steps {
               echo 'Running build automation'
               sh './gradlew build --no-daemon'
               archiveArtifacts artifacts: 'dist/trainSchedule.zip'
           }
       }
    stage('Deploy to staging'){
        when {
          branch 'master'
          }
        steps {
        withCredentials([usernamePassword(credentialsId: 'webserver_login', passwordVariable: 'USERPASS', usernameVariable: 'USERNAME')]) {
        sshPublisher(
          publishers: [
            sshPublisherDesc(
              configName: 'Staging', 
              sshCredentials: [
                username: "$USERNAME",
                password: "$PASSWORD"
              ],
              transfers: [
                  sshTransfer(
                    execCommand: 'sudo /usr/bin/systemctl stop train-schedule && rm -rf  /opt/train-schedule && unzip -d /opt/train-schedule /tmp/train-schedule.zip && sudo /usr/bin/systemctl start train-schedule',
                    remoteDirectory: '/tmp/train-schedule.zip', 
                    removePrefix: 'dist', 
                    sourceFiles: 'dist/*.zip'
                      )
                    ], 
                 )
              ] 
          )
        }
    }
}
    stage('Deploy to production') {
     when {
       branch 'master'
       }
     steps {
     input 'Deploy to production ?'
     milestone label: 'build ready', ordinal: 1
     withCredentials([usernamePassword(credentialsId: 'webserver_login', passwordVariable: 'USERPASS', usernameVariable: 'USERNAME')]) {
     sshPublisher(
       publishers: [
       sshPublisherDesc(
        configName: 'Production', 
        sshCredentials: [
          username: "$USERNAME",
          password: "$PASSWORD"
        ],
        transfers: [
           sshTransfer(
             execCommand: 'sudo /usr/bin/systemctl stop train-schedule && rm -rf  /opt/train-schedule && unzip -d /opt/train-schedule /tmp/train-schedule.zip && sudo /usr/bin/systemctl start train-schedule', 
             remoteDirectory: '/tmp/train-schedule.zip', 
             removePrefix: 'dist', 
             sourceFiles: 'dist/*.zip'
                     )
                   ] 
                 )
              ] 
            ) 
          }
        }
     }
   }
 }     
  
