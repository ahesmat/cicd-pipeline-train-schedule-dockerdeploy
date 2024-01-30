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
        stage('Build Docker Image')
        {
         steps {
             script {
                 def myImage = docker.build("ahesmat/myapp:latest")
                 myImage.inside{
                     sh 'echo $(curl localhost:8080)'
                 }
             }
         }
        }
    }
}
