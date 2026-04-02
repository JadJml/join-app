pipeline {
    agent { label 'Jenkisn-Agent' }
    tools {
        jdk 'Java17'
        maven 'Maven3'

    }
       environment {
	    APP_NAME = "join-app"
            RELEASE = "0.0.1"
            DOCKER_USER = "jadweb"
            DOCKER_PASS = credentials("token-msr")   // ID des credentials Docker dans Jenkins
            IMAGE_NAME = "${DOCKER_USER}" + "/" + "${APP_NAME}"
            IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"
    }
    stages{
        stage("Cleanup Workspace"){
                steps {
                cleanWs()
                }
        }
        stage("Checkout from SCM"){
                steps {
                    git branch: 'main', credentialsId: 'github-TOKEN', url: 'https://github.com/JadJml/join-app.git'
                }
        }
        stage("Build Application"){
            steps {
                sh "mvn clean package"
            }
       }
       stage("Test Application"){
           steps {
                 sh "mvn test"
           }
       }
  
      stage ("build Docker Image ")
          {
            steps {
                script {
                    docker.withRegistry('',DOCKER_PASS) {
                        docker_image = docker.build "${IMAGE_NAME}"
                    }
                      }

                   }
             
          }

    }
}