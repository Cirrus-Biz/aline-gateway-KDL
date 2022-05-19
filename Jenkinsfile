pipeline {
    environment {
        DOCKERHUB_CREDENTIALS=credentials("Dockerhub")
    }
    agent any

    tools {
        maven "MAVEN"
    }

    stages {
        stage('SonarQube Analysis') {
            steps{
                def mvn = tool 'Default Maven';
                withSonarQubeEnv() {
                bat "${mvn}/bin/mvn clean verify sonar:sonar -Dsonar.projectKey=Sonarqube-gateway"
    }
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