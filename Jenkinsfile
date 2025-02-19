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
		sh 'docker run -t gesellix/trufflehog --json https://github.com/jambarushi/webapp.git > trufflehog'
		sh 'cat trufflehog'
	    }
	    }
    
    stage ('Source-Composition-Analysis') {
		steps {
		     sh 'rm owasp-* || true'
		     sh 'wget https://raw.githubusercontent.com/devopssecure/webapp/master/owasp-dependency-check.sh'	
		     sh 'chmod +x owasp-dependency-check.sh'
		     sh 'bash owasp-dependency-check.sh'
		     sh 'cat /var/lib/jenkins/OWASP-Dependency-Check/reports/dependency-check-report.xml'
		}
	}
	  
    stage ('SAST') {
		steps {
		withSonarQubeEnv('sonar') {
			sh 'mvn sonar:sonar'
			sh 'cat target/sonar/report-task.txt'
		       }
		}
	}  
	  
    stage ('Deploy-To-Tomcat') {
            steps {
           sshagent(['tomcat']) {
                sh 'scp -o StrictHostKeyChecking=no target/*.war ubuntu@35.183.97.236:/home/ubuntu/Downloads/tomcat/apache-tomcat-8.5.43/webapps/webapp.war'
              }      
           }       
    }
    
    stage ('DAST') {
		  
	  steps {
	        sshagent(['zap']) {
		       sh 'ssh -o StrictHostKeyChecking=no ubuntu@99.79.6.198 "docker run -t owasp/zap2docker-stable zap-baseline.py -t http://35.183.97.236:8080/webapp/" || true'
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
