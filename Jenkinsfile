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
              app.push("${env.BUILD_NUMBER"})
              app.push("latest")
            }
          }
        }
      }
    }
}

