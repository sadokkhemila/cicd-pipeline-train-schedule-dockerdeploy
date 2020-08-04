pipeline {
  agent any
    stages {
        stage('build') {
            steps {
                 echo 'Running build automation'
                 sh './gradlew build --no-daemon'
                 archiveArtifacts artifacts: 'dist/trainSchedule.zip'
            }
        }
        stage('build docker image') {
            when {
              branch 'master'
            }
            steps {
              script {
                app = docker.build("sadokkhemila/pipdocker")
                app.inside {
                 sh 'echo $(curl localhost:8080)' 
                }
              }
            }
            }
      stage('Push docker image') {
        when {
         branch 'master' 
        }
        steps {
          script {
            docker.withRegistry('https://registry.hub.docker.com', 'docker_hub_login') {
              app.push("${env.BUILD_NUMBER}")
              app.push("latest")
            }
          }
        }
      }
      stage('DeployToProduction') {
            when {
                branch 'master'
            }
            steps {
                input 'Deploy to Production?'
                milestone(1)
                withCredentials([usernamePassword(credentialsId: 'webserver', usernameVariable: 'USERNAME', passwordVariable: 'USERPASS')]) {
                    script {
                        sh "sshpass -p '$USERPASS' -v ssh -o StrictHostKeyChecking=no $USERNAME@$prod_ip \"docker pull sadokkhemila/pipdocker:${env.BUILD_NUMBER}\""
                        try {
                            sh "sshpass -p '$USERPASS' -v ssh -o StrictHostKeyChecking=no $USERNAME@$prod_ip \"docker stop pipdocker\""
                            sh "sshpass -p '$USERPASS' -v ssh -o StrictHostKeyChecking=no $USERNAME@$prod_ip \"docker rm pipdocker\""
                        } catch (err) {
                            echo: 'caught error: $err'
                        }
                        sh "sshpass -p '$USERPASS' -v ssh -o StrictHostKeyChecking=no $USERNAME@$prod_ip \"docker run --restart always --name pipdocker -p 8080:8080 -d sadokkhemila/pipdocker:${env.BUILD_NUMBER}\""
                    }
                }
            }
        }
    }
}

