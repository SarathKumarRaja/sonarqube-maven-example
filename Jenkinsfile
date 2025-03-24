pipeline {
   agent any
   environment {
	PATH = "/opt/maven/bin:$PATH"
   }
    stages {
       stage("Build") {
	  steps {
		echo "-------------------------build started----------------"
		
		sh 'mvn clean deploy -Dmaven.test.skip=true'

		echo "-----------------------build complete-----------------"
	   }
        }
     	stage('Test') {
           steps {
		echo "-------------------unit test started------------------"

		sh 'mvn surefire-report:report'

		echo "------------------unit test completed-----------------"
           }
         }
	stage('SonarQube Analysis') {
              environment {
		scannerHome = tool 'sonarQube-scanner'
          }
        steps {
             withSonarQubeEnv('sonarqube-server') {
 		sh "${scannerHome}/bin/sonar-scanner"
		}
	    }
	}

	stage("Quality Gate") {
	  steps {
	    script {
		timeout(time: 1, unit: 'HOURS') {
        	  def qg = waitForQualityGate()
                  if (qg.status != 'OK') {
                      error "Pipeline aborted due to quality f=gate Faliure: ${qg.status}
                     }
                 }
              }
          }
       }
                         
    }
}

