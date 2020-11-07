pipeline {
    agent any
    tools {
        maven 'maven'
        jdk 'jdk'
    }
    stages {
        stage ('Initialize') {
            steps {
                sh '''
                    echo "PATH = ${PATH}"
                    echo "M2_HOME = ${M2_HOME}"
                '''
            }
        }

        stage ('Build') {
            steps {
                sh 'mvn clean package dependency:purge-local-repository'
            }
        }


        stage ('Deploy to Octopus') {
            steps {
                withCredentials([string(credentialsId: 'OctopusAPIKey', variable: 'APIKey')]) {
                    sh """
                        ${tool('Octo CLI')}octo push --package target/petclinic.2.3.1.BUILD-SNAPSHOT.jar --replace-existing --server https://samples.octopus.app --apiKey ${APIKey} --space Spaces-203                       
                    """
                }
            }
        }
    }
}
