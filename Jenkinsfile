pipeline {
    agent any
    stages{
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