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
        
        stage ('Pack Flyway') {
            steps {
                    sh """
                        echo ""
                        ${tool('Octo CLI')}octo pack --id flyway --format zip --version 2.3.1 --outFolder target --basePath flyway                   
                    """
                }
            }
        
        stage ('Deploy to Octopus') {
            steps {
                withCredentials([string(credentialsId: 'OctopusAPIKey', variable: 'APIKey')]) {
                    sh """
                        ${tool('Octo CLI')}octo push --package target/petclinic.2.3.1.war --replace-existing --server https://samples.octopus.app --apiKey ${APIKey} --space Spaces-203
                        ${tool('Octo CLI')}octo push --package target/flyway.2.3.1.zip --replace-existing --server https://samples.octopus.app --apiKey ${APIKey} --space Spaces-203                       
                    """
                }
            }
        }
    }
}
