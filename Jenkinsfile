pipeline{

    agent any 

    parameters{

        string(name: 'region', defaultValue: 'us-east-1', description: 'Choose AWS Region')
        string(name: 'cluster', defaultValue: 'deploy-cluster', description: 'Choose AWS Clustername')
    }

    environment{
        ACCESS_KEY = credentials('AWS_ACCESS_KEY_ID_')
        SECRET_KEY = credentials('AWS_SECRET_KEY_ID_')
    }

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
    */
        stage('docker image build'){
            steps{
                script{

                    sh """
                    aws configure set access_key_id "$ACCESS_KEY"
                    aws configure set secret_key_id "$SECRET_KEY"
                    aws configure set region "${params.region}"
                    aws ecr get-login-password --region ${params.region} | docker login --username AWS --password-stdin 996864587356.dkr.ecr.${params.region}.amazonaws.com
                    docker build -t webapp1 .
                    docker tag webapp1:latest 996864587356.dkr.ecr.${params.region}.amazonaws.com/webapp1:latest
                    docker push 996864587356.dkr.ecr.${params.region}.amazonaws.com/webapp1:latest
                    """
                }
            }
        }
        stage('EKS Module'){
            steps{

                script{
                        dir('eks_module'){
                    sh """
                    terraform init
                    terraform plan -var "access_key=$ACCESS_KEY" -var "secret_key=$SECRET_KEY" --var-file="./config/terraform.tfvars"
                    terraform apply -var "access_key=$ACCESS_KEY" -var "secret_key=$SECRET_KEY" --var-file="./config/terraform.tfvars" --auto-approve
                    """
                        }
                }
            }
        }
         stage('Connect with eks cluster'){
              steps{

                script{

                    sh """
                    aws configure set access_key_id "$ACCESS_KEY"
                    aws configure set secret_key_id "$SECRET_KEY"
                    aws configure set region "${params.region}"

                    aws eks --region "${params.region}" update-kobeconfig --name "${params.cluster}"

                    """

                }
            }
        }
    }
}