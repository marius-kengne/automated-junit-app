pipeline {
    agent{
        docker {
            image 'node:alpine'
            args '-e NPM_CONFIG_LOGLEVEL=info -p 8000:8000'
        }
    }
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