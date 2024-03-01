pipeline {
    agent any
    environment {
        IMAGE_NAME="course-catalog"
        IMAGE_TAG="0.${BUILD_NUMBER}"
    }

    stages{

        stage('Build'){
            steps{
                script{
                    image = docker.build("${IMAGE_NAME}:${IMAGE_TAG}")
                }
            }
        }

        stage('Executando Teste Unitario'){
            steps{
                script{
                    image.inside("-v ${WORKSPACE}:/courseCatalog"){
                        sh "nosetests --with-xunit --with-coverage --cover-package=project test_users.py"
                    }
                }
            }
        }
        stage('SonarQube Analysis'){
            steps{
                script{
                    def scannerPath = tool 'SonarScanner'
                    withSonarQubeEnv('SonarQube'){
                        sh "${scannerPath}/bin/sonar-scanner -Dsonar.projectKey=courseCatalog -Dsonar.sources=. -Dsonar.host.url=http://192.168.88.20:9000" 
                    }
                    
                    
                }
            }
        }
        stage('SonarQube Analysis Result'){
            steps{
                script{
                    timeout(time: 30, unit: 'SECONDS'){
                        waitForQualityGate abortPipeline: true
                    }
                }
            }
        }

        stage('Push image'){
            steps{
                script{
                    docker.withServer("http://192.168.88.20:8082", 'b374f54f-2715-4723-b845-4e87f8bbbfea' ){
                        image.push()
                    }
                }
            }
        }
    }

}
