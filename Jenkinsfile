def imageName = 'kodalib4u.jfrog.io/kodalib4u-docker-local/ttrend'
def version   = '2.1.2'
def registry = 'https://kodalib4u.jfrog.io'
pipeline
{
    agent 
    {
        node 
        {
           label "maven" 
        }
       
    }
    environment{
        PATH = "/opt/apache-maven-3.9.3/bin:$PATH"
    }
    stages 
    {
        stage('Build Code') {
            steps {
                sh 'mvn clean deploy'
            }
        }
        stage('Test code') {
            steps {
                echo ".........Unit Test Started......."
                sh 'mvn surefire-report:report'
                echo ".........Unit Test completed......"
            }
        }
        stage('SonarQube analysis') {
           environment {
            scannerHome = tool 'kodalib4u-sonarqube-scanner'
            }
            steps {
               withSonarQubeEnv('sonarqube-server')
                { // If you have configured more than one global server connection, you can specify its name
                 sh "${scannerHome}/bin/sonar-scanner"
                }  
            }
        }
        stage("Quality Gate")
        {
            steps
            {
             script
             {
               timeout(time: 1, unit: 'HOURS') 
               { // Just in case something goes wrong, pipeline will be killed after a timeout
                 def qg = waitForQualityGate() // Reuse taskId previously collected by withSonarQubeEnv
                 if (qg.status != 'OK') 
                 {
                   error "Pipeline aborted due to quality gate failure: ${qg.status}"
                 }
               }
             }
           }
        }
              
        stage(" Docker Build ")
        {
        steps 
           {
            script 
                {
            echo '<--------------- Docker Build Started --------------->'
            app = docker. build(imageName+":"+version)
            echo '<--------------- Docker Build Ends --------------->'
                }
            }
        }

        stage (" Docker Publish ")
        {
         steps 
           {
            script 
               {
               echo '<--------------- Docker Publish Started--------------->'  
                docker.withRegistry(registry, 'artifacts-cred')
                {
                    app.push()
                }    
               echo '<--------------- Docker Publish Ended --------------->'  
                } 
           }
       }
       stage('Deploy Application in K8s Cluster using Helm Charts') 
       {
         steps 
           {
              echo '<--------------- Helm Deploy Started --------------->'
	                     sh 'helm install ttrend ttrend-0.1.0.tgz'
               echo '<--------------- Helm deploy Ends --------------->'
           }
        }
    }
}

