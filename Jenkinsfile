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
            when {
                branch 'master'
            }
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
            steps{
                input(message: 'Are we good to go', ok: 'Deploy to production now')
                milestone(1)
            withCredentials([usernamePassword(credentialsId: 'webserver_login', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                script{
                 sh "sshpass -p '$PASSWORD' -v ssh -o StrictHostKeyChecking=no $USERNAME@${env.prod_ip} \"docker pull ahesmat/myapp:${env.BUILD_NUMBER}\""         
                }
        }
        }
    }
}
