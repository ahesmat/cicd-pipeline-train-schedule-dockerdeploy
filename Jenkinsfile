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
            when {
                branch 'master'
            }
         steps {
             script {
                 myImage = docker.build("ahesmat/myapp:latest")
                 myImage.inside{
                     sh 'echo $(curl localhost:8080)'
                 }
             }
         }
        }
        stage('Push to Dockerhub'){
              steps {
                  script {
                  docker.withRegistry('https://registry.hub.docker.com/','docker_hub_login'){
                   myImage.push('latest')
                   myImage.push("${env.build_number}")   
                   
                                }             
                  }
              }
              }
        stage('Deploy to Production')
        {
            when {
                branch 'master'
            }
            withCredentials([usernamePassword(credentialsId: 'webserver_login', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                script{
                 sh "sshpass -p '$PASSWORD' -v ssh -o StrictHostKeyChecking=no $USERNAME@${env.prod_ip}
                    \"docker run --name train-schedule -p 8080:8080 -d ahesmat/myapp:${env.BUILD_NUMBER}\""   
                }
            }
        }
    }
}
