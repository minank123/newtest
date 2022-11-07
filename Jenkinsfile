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
                 nexusArtifactUploader artifacts: [
				   [ artifactId: 'maven-project', 
				     classifier: '', 
					 file: 'target/Maven Project-1.0-SNAPSHOT.war', 
					 type: 'war',
				   ]
				 ] 
				 credentialsId: 'nexus', 
				 groupId: 'com.example.maven-project', 
				 nexusUrl: 'ec2-13-233-7-212.ap-south-1.compute.amazonaws.com:8081', 
				 nexusVersion: 'nexus3', 
				 protocol: 'http', 
				 repository: 'http://ec2-13-233-7-212.ap-south-1.compute.amazonaws.com:8081/repository/a1/', 
				 version: '1.0-SNAPSHOT'
                }    
                    
            }
        }


    }


}