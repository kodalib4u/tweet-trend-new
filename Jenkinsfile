pipeline {
    agent {
        node {
           label "maven" 
        }
       
    }
    environment{
        PATH = "/opt/apache-maven-3.9.3/bin:$PATH"
    }
    stages {
        stage('Build Code') {
            steps {
                sh 'mvn clean deploy'
            }
        }
        stage('SonarQube analysis') {
           environment {
            scannerHome = tool 'kodalib4u-sonarqube-scanner'
            }
            steps {
               withSonarQubeEnv('kodalib4u-sonarqube-server')
                { // If you have configured more than one global server connection, you can specify its name
                 sh "${scannerHome}/bin/sonar-scanner"
                }  
            }
        }
        }
    }

