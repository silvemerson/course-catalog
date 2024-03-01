pipeline {
    agent any
    environment {
        IMAGE_NAME="course-catalog"
        IMAGE_TAG="0.${BUILD_NUMBER}"
    }

    stages{
        stage('SonarQube Analysis'){
            steps{
                script{
                    def scannerPath = tool 'SonarScanner'{
                        sh "${scannerPath}/bin/sonar-scanner -Dsonar.projectKey=courseCatalog -Dsonar.sources=. -Dsonar.host.url=http://192.168.88.20:9000 -Dsonar.login=sqp_eeeef43e69619b5d95a1bc0c74d5f3f54b09f703" 

                    }
                }
            }
        }
    }

}
