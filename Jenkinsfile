pipeline {
    agent any
    environment {
        IMAGE_NAME="course-catalog"
        IMAGE_TAG="0.${BUILD_NUMBER}"
        NEXUS_REPO="192.168.88.20:8082"
        HTTP_PROTO="http://"
        DOCKER_REGISTRY="${HTTP_PROTO}${NEXUS_REPO}"
        NAMESPACE_HOMOLOG="homolog"
        NAMESPACE_PROD="prod"
        KUBECONFIG = credentials('k3s_kubeconfig')
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
                    docker.withRegistry('http://192.168.88.20:8082', 'b374f54f-2715-4723-b845-4e87f8bbbfea' ){
                        image.push("${IMAGE_TAG}")
                        image.push("latest")
                    }
                }
            }
        }

        stage ('Inicializando o Kubecofig'){
            steps{
                sh 'echo $KUBECONFIG'
                // sh 'mkdir .kubeconfig'
                sh 'cat $KUBECONFIG > .kubeconfig/config'
            }
        }
        
        stage('Deploy em Homologação'){
            steps{
                script{
                    sh 'kubectl -n ${NAMESPACE_HOMOLOG} apply -f manifest '
                }

            }
        }
    }
    post{
        success {
            echo "Pipeline executada com Sucesso"
        }

        failure{
            echo "Pipeline executada com Falha"
        }
        // cleanup{
        //     sh "docker rmi -f ${NEXUS_REPO}/${IMAGE_NAME}:${IMAGE_TAG}"
        //     sh "docker rmi -f ${NEXUS_REPO}/${IMAGE_NAME}:latest"
        // }
    }
}
