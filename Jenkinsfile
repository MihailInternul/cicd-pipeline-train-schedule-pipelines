pipeline {
  agent any
  stages {
    stage('DeployToStaging') {
    when {
        branch 'master'
    }
    steps {
        withCredentials([usernamePassword(credentialsId: 'webserver_login', usernameVariable: 'USERNAME', passwordVariable: 'USERPASS')]) {
            script {
                sshPublisher(
                    failOnError: true,
                    publishers: [
                        sshPublisherDesc(
                            configName: 'staging',
                            transfers: [
                                sshTransfer(
                                    sourceFiles: 'dist/trainSchedule.zip',
                                    removePrefix: 'dist/',
                                    remoteDirectory: '/tmp',
                                    execCommand: '''
                                        sudo /usr/bin/systemctl stop train-schedule &&
                                        sudo rm -rf /opt/train-schedule/* &&
                                        sudo unzip /tmp/trainSchedule.zip -d /opt/train-schedule &&
                                        sudo /usr/bin/systemctl start train-schedule
                                    '''.stripIndent()
                                )
                            ],
                            sshCredentials: [
                                username: "$USERNAME",
                                password: "$USERPASS"
                            ]
                        )
                    ]
                )
            }
        }
    }
}
