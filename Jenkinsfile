@Library("Jenkins_shared_libraries") _

pipeline {
    agent any

    environment {
        IMAGE_NAME      = "php-app"
        IMAGE_TAG       = "${BUILD_NUMBER}"     // avoid using BUILD_TAG (Jenkins has built-in BUILD_TAG)
        DOCKERHUB_CREDS = "dockerhubcred"
    }

    stages {
        stage("Code Checkout") {
            steps {
                script {
                    code_clone("https://github.com/alisarfraz13/php-application-with-CICD.git", "main")
                }
            }
        }

        stage("Build") {
            steps {
                script {
                    buildDockerImage(
                        imageName: env.IMAGE_NAME,
                        buildTag:  env.IMAGE_TAG
                    )
                }
            }
        }

        stage("Push") {
            steps {
                script {
                    pushDockerImage(
                        credentialsId: env.DOCKERHUB_CREDS,
                        imageName:      env.IMAGE_NAME,
                        buildTag:       env.IMAGE_TAG   // IMPORTANT: correct key name
                    )
                }
            }
        }

        stage("Deploy") {
            steps {
                script {
                    cleanAndDeploy(
                        credentialsId: env.DOCKERHUB_CREDS,
                        imageName:      env.IMAGE_NAME,
                        newBuildTag:    env.IMAGE_TAG,
                        containerName:  "php-app-container"
                    )
                }
            }
        }

        stage("Status Check") {
            steps {
                script {
                    statusCheck(imageName: env.IMAGE_NAME)
                }
            }
        }
    }

    post {
        always { cleanWs() }
        succes
