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
        
    post {
        always {
            archiveArtifacts artifacts: 'build/libs/**/*.war', fingerprint: true
            junit 'build/reports/**/*.xml'
        }

        stage ('Deploy to Octopus') {
            steps {
                withCredentials([string(credentialsId: 'OctopusAPIKey', variable: 'APIKey')]) {
                    sh """
                        ${tool('Octo CLI')}/Octo push --package target/demo.0.0.1-SNAPSHOT.war --replace-existing --server https://youroctopusserver --apiKey ${APIKey}                        
                    """
                }
            }
        }
    }
}
