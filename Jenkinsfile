pipeline {
    environment {
        DOCKERHUB_CREDENTIALS=credentials("Dockerhub")
    }
    agent any

    tools {
        maven "MAVEN"
    }

    stages {

        stage("Sonarqube tests"){
            steps{
                def scanner = 'SonarScanner 4.0';
                withSonarQubeEnv('Sonarqube')
                bat "${scannerHome}bin/sonar-scanner"
            }
        }


        stage("Build MVN") {
            steps {
                bat "mvn -Dmaven.test.failure.ignore=true clean package"
            }
        }


        stage("Build Docker"){
            steps{
                bat "docker build -t laxwalrus/capstone-gateway:$BUILD_NUMBER ."

            }            
        }


        stage("login to docker"){
            steps{
                bat "echo $DOCKERHUB_CREDENTIALS_PSW| docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin"
            }
        }

        stage("Deploy Docker"){
            steps{
                bat "docker push laxwalrus/capstone-gateway:$BUILD_NUMBER"
            }
        }
            


        stage("Cleaning"){
            steps{
                bat "docker logout"
            }
        }
        
        stage('Archive') {
            steps {
            archiveArtifacts artifacts: 'target/*.jar', followSymlinks: false
            }

        }
    }
}