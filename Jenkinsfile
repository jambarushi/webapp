pipeline {
  agent any 
  tools {
    maven 'Maven'
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
   
    stage ('Check-Git-Secrets') {
		    steps {
	        sh 'rm trufflehog || true'
		sh 'docker pull gesellix/trufflehog'
		sh 'docker run -t gesellix/trufflehog --json https://github.com/devopssecure/webapp.git > trufflehog'
		sh 'cat trufflehog'
	    }
	    }
    
    stage ('Deploy-To-Tomcat') {
            steps {
           sshagent(['tomcat']) {
                sh 'scp -o StrictHostKeyChecking=no target/*.war ubuntu@35.183.97.236:/home/ubuntu/Downloads/tomcat/apache-tomcat-8.5.43/webapps/webapp.war'
              }      
           }       
    }
    
    
    stage ('Build') {
      steps {
      sh 'mvn clean package'
       }
    }
    
   
  }
}
