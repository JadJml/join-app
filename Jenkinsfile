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
        DOCKER_PASS = 'token-dockerhub'   // Jenkins credentials ID
        IMAGE_NAME = "${DOCKER_USER}/${APP_NAME}"
        IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"
    }

    stages {

        stage("Cleanup Workspace") {
            steps {
                cleanWs()
            }
        }

        stage("Checkout from SCM") {
            steps {
                git branch: 'main',
                    credentialsId: 'github-TOKEN',
                    url: 'https://github.com/JadJml/join-app.git'
            }
        }

        stage("Build Application") {
            steps {
                sh "mvn clean package -DskipTests"
            }
        }

        stage("Run Tests") {
            steps {
                sh "mvn test"
            }
        }

        stage("Build Docker Image") {
            steps {
                script {
                    dockerImage = docker.build("${IMAGE_NAME}:${IMAGE_TAG}")
                }
            }
        }

        stage("Push Docker Image") {
            steps {
                script {
                    docker.withRegistry('', DOCKER_PASS) {
                        dockerImage.push()
                        dockerImage.push("latest")
                    }
                }
            }
        }

        stage("Run Container") {
            steps {
                script {
                    sh """
                    docker stop ${APP_NAME} || true
                    docker rm ${APP_NAME} || true

                    docker run -d \
                      --name ${APP_NAME} \
                      -p 9095:8080 \
                      ${IMAGE_NAME}:${IMAGE_TAG}
                    """
                }
            }
        }

        stage("Cleanup Docker Images") {
            steps {
                sh "docker rmi ${IMAGE_NAME}:${IMAGE_TAG} || true"
            }
        }
    }

    post {
        success {
            echo "Build terminé : ${IMAGE_NAME}:${IMAGE_TAG}"
            echo "Application : http://localhost:9095"
        }
        failure {
            echo "❌ Échec du pipeline"
        }
        always {
            cleanWs()
        }
    }
}