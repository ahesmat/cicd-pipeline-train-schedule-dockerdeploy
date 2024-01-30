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
                 def myImage = docker.build("ahesmat/myImage:latest")
                 myimage.inside{
                     sh 'curl localhost:/8080'
                 }
             }
         }
        }
    }
}
