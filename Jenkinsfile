pipeline {
    agent { label 'Jenkins-Agent' }
    tools {
        jdk 'JDK'
        maven 'Maven3'
    }
    environment {
        APP_NAME = "register-app-pipeline"
        RELEASE = "1.0.0"
        DOCKER_USER = "chennareddy2912@gmail.com"
        DOCKER_PASS = credentials('Docker-hub')  
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
                git branch: 'main', credentialsId: 'github', url: 'https://github.com/Chenna6301/register-app.git'
            }
        }

        stage("Build Application") {
            steps {
                sh "mvn clean package"
            }
        }

        stage("Test Application") {
            steps {
                sh "mvn test"
            }
        }
        
        stage("SonarQube Analysis") {
            steps {
                script {
                    withSonarQubeEnv('sonarqube') { 
                        sh "mvn sonar:sonar"
                    }
                }
            }
        }

        stage("Quality Gate") {
            steps {
                script {
                    waitForQualityGate abortPipeline: false
                }
            }
        }
        
        stage("Build & Push Docker Image") {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'docker-hub') {  // Use Docker Hub registry and Jenkins credentials
                        def docker_image = docker.build("${IMAGE_NAME}")
                        docker_image.push("${IMAGE_TAG}")
                        docker_image.push('latest')
                    }
                }
            }
        }
    }
}
