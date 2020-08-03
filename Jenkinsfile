pipeline {
    agent any
    stages {
        stage('build') {
            steps {
                  echo 'Running build automation'
                  sh './gradlew build --no-daemon'
                  archivesArtifats artifacts: 'dist/trainshedule1.zip'
            }
        }
    }
}
