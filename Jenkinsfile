pipeline {
    agent any
    tools {
        maven 'Maven'
    }
    environment {
        IMAGE_NAME = 'synyster008/springboot:latest'
        IMAGE_TAG = 'latest'
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
    }
}
