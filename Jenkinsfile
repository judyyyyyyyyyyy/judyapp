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
        stage('Quality code analysis'){

            steps{

                script {

                    withSonarQubeEnv(credentialsId: 'sonarqube-api') {
                        sh 'mvn clean package sonar:sona'
                    }
                }
            }
        }
    }
}