pipeline {
    
	agent any
/*	
	tools {
        maven "maven3"
    }
*/	
  
	
    stages{
        
        stage('BUILD'){
            steps {
                sh 'mvn clean install -DskipTests'
            }
            post {
                success {
                    echo 'Now Archiving...'
                    archiveArtifacts artifacts: '**/target/*.war'
                }
            }
        }

	    stage('UNIT TEST'){
            steps {
                sh 'mvn test'
            }
        }

	    stage('INTEGRATION TEST'){
            steps {
                sh 'mvn verify -DskipUnitTests'
            }
        }
		
     
        stage("Publish to Nexus Repository Manager") {
            steps {
                script {
                 nexusArtifactUploader artifacts:[
                    [ 
                      artifactId: 'vprofile', 
                      classifier: '', 
                      file: 'vprofile-4.0.0.war', 
                      type: 'war'
                   ]
                 ], 
                credentialsId: '7fc33072-8038-47ed-91a6-b6064a352a14', 
                groupId: 'com.visualpathit', 
                nexusUrl: 'ip-172-31-36-248.ap-south-1.compute.internal:8081', 
                nexusVersion: 'nexus3', 
                protocol: 'http', 
                repository: 'maven-releases', 
                version: '4.0.0'
                }    
                    
            }
        }


    }


}