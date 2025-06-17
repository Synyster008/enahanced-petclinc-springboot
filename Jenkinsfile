pipeline {
    agent any
    tools {
        maven 'Maven'
    }
    stages {
        stage('Checkout From Git') { 
            steps {
                git branch: 'prod', url: 'https://github.com/Synyster008/enahanced-petclinc-springboot.git'
            }
        }
        stage('Maven Compile') { 
            steps {
                echo 'This Maven Compile Stage'
                sh 'mvn compile'
            }
        }
        stage('Maven Test') { 
            steps {
                echo 'This Maven Test Stage'
                sh 'mvn test'
            }
        }
        stage('Trivy Scan') { 
            steps {
                echo 'This Trivy Scan Stage'
                sh 'trivy fs --format table --exit-code 1 --severity HIGH,CRITICAL .'
            }
        }
        stage ('SonarQube Analysis') {
            environment {
                    SCANNER_HOME = tool 'SonarQube Scanner'
                }
            steps {
                echo 'This SonarQube Analysis Stage'
                withSonarQubeEnv('SonarQube') {
                    sh '''
                        $SCANNER_HOME/bin/sonar-scanner \
                        -Dsonar.organization=synyster008 \
                        -Dsonar.projectName=springboot \
                        -Dsonar.projectKey=synyster008_springboot \
                        -Dsonar.java.binaries= \
                        -Dsonar.exclusions=**/trivy-report.txt
                    '''
                }
            }
        }
        stage('SonarQube Quality Gate') {
            steps {
                echo 'This SonarQube Quality Gate Stage'
                steps{
                    timeout(time: 1, unit: 'MINUTES') {
                        waitForQualityGate abortPipeline: true
                }
            }
        }
        }
    }
}
