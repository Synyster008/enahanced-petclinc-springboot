pipeline {
    agent { label 'linux-agent'}
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
    }
}
