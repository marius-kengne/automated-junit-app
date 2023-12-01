pipeline {

    agent any

    environment  {
        JIRA_XRAY_TOKEN = credentials('jira-xray-key')
        JIRA_XRAY_USER = credentials('jira-xray-user')
    }

    parameters {
       string(name:'JIRA_BASE_URL', defaultValue: 'https://mariuskengne.atlassian.net')
       string(name:'JIRA_PROJECT_KEY', defaultValue: 'CL')
       string(name:'PROJECT_ISSUES_KEY', defaultValue: 'CL-8')
    }

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

        stage('Check and Install Tools') {
            steps {
                script {
                    // Vérification de xmlstarlet
                    def xmlStarletInstalled = sh(script: 'command -v xmlstarlet', returnStatus: true)
                    if (xmlStarletInstalled != 0) {
                        echo "xmlstarlet n'est pas installé. Installation en cours..."
                        sh "sudo apt-get update && sudo apt-get install -y xmlstarlet"
                    } else {
                        echo "xmlstarlet est déjà installé."
                    }

                    // Vérification de curl
                    def curlInstalled = sh(script: 'command -v curl', returnStatus: true)
                    if (curlInstalled != 0) {
                        echo "curl n'est pas installé. Installation en cours..."
                        sh "sudo apt-get update && sudo apt-get install -y curl"
                    } else {
                        echo "curl est déjà installé."
                    }
                }
            }
        }

        stage('Update Xray in Jira') {
            steps {
                script {
                    def inputFile = "${WORKSPACE}/target/surefire-reports/TEST-Calculator.xml"
                    def outputFile = "${WORKSPACE}/target/surefire-reports/output.json"

                    // Utilisation de xmlstarlet pour convertir XML en JSON
                    sh "xmlstarlet fo --json ${inputFile} > ${outputFile}"

                    // Affichage du contenu du fichier JSON
                    sh "cat ${outputFile}"

                    // Appel de la fonction pour mettre à jour Xray avec le fichier JSON
                    updateXrayWithJson("${outputFile}")
                }
            }
        }

    }
}


def updateXrayWithJson(jsonFile) {

    // URL de l'API Xray pour mettre à jour les résultats de test
    def xrayApiUrl = "${JIRA_BASE_URL}/rest/raven/1.0/api/testexec/${PROJECT_ISSUES_KEY}/test"

    // Utilisation de curl pour envoyer le fichier JSON à Xray
    sh "curl -u '${JIRA_XRAY_USER}:${JIRA_XRAY_TOKEN}' -H 'Content-Type: application/json' -X POST -d @${jsonFile} ${xrayApiUrl}"
}
