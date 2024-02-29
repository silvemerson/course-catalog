pipeline {
    agent any
    
    environment {
        IMAGE_NAME="courseCatalog"
        IMAGE_TAG="0.${BUILD_NUMBER}"
    }

    stages{
        stage("Build"){
            steps{
                script{
                    image = docker.build("${IMAGE_NAME}:${IMAGE_TAG}")
                }
            }
        }
    }

}
