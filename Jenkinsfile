pipeline {
    agent any
    stages{

        stage('Get Maven'){
            steps {
                script {
                    def mavenVersion = '3.9.4'  // Mettez à jour la version de Maven selon vos besoins
                    def mavenHome = tool name: "Maven3", type: 'maven'
                    env.PATH = "${mavenHome}/bin:${env.PATH}"
                    echo "Maven installed at ${mavenHome}"
                }
            }
        }
        stage('clean project'){
            steps{
                sh 'mvn clean'
            }
        }

        stage('build'){
            steps{
                sh 'mvn compile'
            }
        }

        stage('run test'){
            steps{
                sh 'mvn test'
            }
        }

    }
}