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
    }
}
