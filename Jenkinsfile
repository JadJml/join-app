pipeline {
    agent { label 'Jenkisn-Agent' }
    tools {
        jdk 'Java17'
        maven 'Maven3'



    }
    stages{
        stage("Cleanup Workspaces"){
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
    }
}
