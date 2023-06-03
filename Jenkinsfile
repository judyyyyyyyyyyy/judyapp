pipeline{

    agent any 

    stages{

        stage('Git Checkout'){

            steps{

                script {

                    git branch: 'devops', url: 'https://github.com/judyyyyyyyyyyy/judyapp.git'
                }
            }
        }
        stage('UNIT Testing'){

            steps{

                script {

                    sh 'mvn test'
                }
            }
        }
        stage('Integration testing'){

            steps{

                script {

                    sh 'mvn verify -DskipUnitTests'
                }
            }
        }
        /*stage('Static code analysis'){

            steps{

                script {

                    withSonarQubeEnv(credentialsId: 'sonarqube-api') {
                        sh 'mvn clean package sonar:sonar'
                    }
                }
            }
        }
        stage('Quality gate status'){
            steps{
                
                script {

                    waitForQualityGate abortPipeline: false, credentialsId: 'sonarqube-api'
                }
            }
        } 
       */ 
        stage('Maven build'){
            steps{

                script {

                    sh 'mvn clean install'
                }
            }
        }
        stage('Nexus artifact upload'){
            steps{

                script {

                    def readPomVersion = readMavenPom file: 'pom.xml'
                    
                    def nexusRepo = readPomVersion.version.endsWith("SNAPSHOT") ? "maven-snapshots" : "maven-releases"

                    nexusArtifactUploader artifacts: [[
                    artifactId: 'springboot', 
                    classifier: '', 
                    file: 'target/Uber.jar', 
                    type: 'jar']], 
                    credentialsId: 'nexus-creds', 
                    groupId: 'com.example', 
                    nexusUrl: '18.208.145.72:8081', 
                    nexusVersion: 'nexus3', 
                    protocol: 'http', 
                    repository: nexusRepo, 
                    version: "${readPomVersion.version}"
                }
            }
        }
        stage('docker image build'){
            steps{
                script{

                    sh """
                    aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 996864587356.dkr.ecr.us-east-1.amazonaws.com
                    docker build -t webapp1 .
                    docker tag webapp1:latest 996864587356.dkr.ecr.us-east-1.amazonaws.com/webapp1:latest
                    docker push 996864587356.dkr.ecr.us-east-1.amazonaws.com/webapp1:latest
                    """
                }
            }
        }
    }
}