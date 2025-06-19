pipeline {
    agent any
    tools {
        maven 'Maven'
    }
    environment {
        IMAGE_NAME = 'springboot'
        IMAGE_TAG = 'latest'
        ACR_NAME = 'synyster008acr'
        ACR_LOGIN_SERVER = "${ACR_NAME}.azurecr.io"
        FULL_IMAGE_NAME = "${ACR_LOGIN_SERVER}/${IMAGE_NAME}:${IMAGE_TAG}"
        TENANT_ID ='519f2c05-b402-40be-888b-b27eea2ef471'
        RESOURCE_GROUP = 'demo-eks-rg'
        CLUSTER_NAME = 'demo-eks'
    }
    stages {
        stage('Checkout From Git') { 
            steps {
                git branch: 'prod', url: 'https://github.com/Synyster008/enahanced-petclinc-springboot.git'
            }
        }
        stage('Trivy Scan') { 
            steps {
                echo 'This Trivy Scan Stage'
                sh 'trivy fs --format table --exit-code 1 --severity HIGH,CRITICAL .'
            }
        }
        // stage ('SonarQube Analysis') {
        //     environment {
        //             SCANNER_HOME = tool 'SonarQube Scanner'
        //         }
        //     steps {
        //         echo 'This SonarQube Analysis Stage'
        //         withSonarQubeEnv('SonarQube') {
        //             sh '''
        //                 $SCANNER_HOME/bin/sonar-scanner \
        //                 -Dsonar.organization=synyster008 \
        //                 -Dsonar.projectName=springboot \
        //                 -Dsonar.projectKey=synyster008_springboot \
        //                 -Dsonar.java.binaries= \
        //                 -Dsonar.exclusions=**/trivy-report.txt
        //             '''
        //         }
        //     }
        // }
        // stage('SonarQube Quality Gate') {
            
                
        //     steps{
        //         echo 'This SonarQube Quality Gate Stage'
        //         timeout(time: 1, unit: 'MINUTES') {
        //             waitForQualityGate abortPipeline: true
        //         }
        //     }
        
        // }

        stage('Maven Package') { 
            steps {
                echo 'This Maven Package Stage'
                sh 'mvn package'
            }
        }
        stage('Docker Build') { 
            steps {
                script{
                    echo 'This Docker Build Stage'
                    docker.build ("$IMAGE_NAME:$IMAGE_TAG")
                }
                
            }
        }
        stage('Login to ACR'){
            steps {
                withCredentials([usernamePassword(credentialsId: 'azurespn', usernameVariable: 'AZURE_USERNAME', passwordVariable: 'AZURE_PASSWORD')]) {
                   echo 'This Login to ACR Stage'
                
                script{
                    sh '''
                    az login --service-principal -u $AZURE_USERNAME -p $AZURE_PASSWORD --tenant $TENANT_ID
                    az acr login --name $ACR_NAME
                    '''
                }
                }
                
            }
        }

        stage('Docker Push') { 
            steps {
                script {
                    echo 'This Docker Push Stage'
                    sh "docker tag $IMAGE_NAME:$IMAGE_TAG $FULL_IMAGE_NAME"
                    sh "docker push $FULL_IMAGE_NAME"
                }
            }
        }
        stage('Jenkins Login to AKS') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'azurespn', usernameVariable: 'AZURE_USERNAME', passwordVariable: 'AZURE_PASSWORD')]) {
                   echo 'This Login to AKS Stage with Jenkins'
                
                script{
                    sh '''
                    az login --service-principal -u $AZURE_USERNAME -p $AZURE_PASSWORD --tenant $TENANT_ID
                    az aks get-credentials --resource-group demo-eks-rg --name demo-eks
                    '''
                }
                }
                
            }
        }
        stage('Deploy to AKS') {
            steps {
                echo 'This Deploy to AKS Stage'
                script {
                    sh '''
                    kubectl apply -f k8s/sprinboot-deployment.yaml
                    kubectl apply -f k8s/service.yaml
                    '''
                }
            }
        }
    }
}
