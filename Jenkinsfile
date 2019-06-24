node {
	try {
	    notifyStarted()
		stage('checkout') {
	        git 'https://github.com/RAMKIDEVOPS/CI-with-Jenkins-in-AWS-Demo.git'
		}
	   	stage('compile') {
	    	withMaven(maven:'MyMaven') {
		        sh 'mvn compile'
		        sh 'mvn clean install'
	        }
	   }
		stage('Build Docker Image'){
           sh 'docker build -t niraimani/devops-casestudy .'
         }
         
         stage('Push to Docker Hub') {
        	 withCredentials([usernamePassword(credentialsId: 'docker-hub', passwordVariable: 'doc_password', usernameVariable: 'doc_username')]) {
                sh "docker login -u=${doc_username} -p=${doc_password}"
             }
        	 sh 'docker push niraimani/devops-casestudy'
        }
	   	stage('Deploy tomcat') {
	        sshagent(['tomcat-dev']) {
	            sh 'scp -o StrictHostKeyChecking=no $WORKSPACE/project/target/project-1.0-RAMA.war root@35.188.92.142:/opt/tomcat/webapps'
	        }
	   	}
	    stage('Email Notification') {
		   notifySuccessful()
		}
	} catch (e) {
    currentBuild.result = "FAILED"
    notifyFailed()
    throw e
  }
}
def notifyStarted() {
	mail subject: "STARTED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
	    body: """
			STARTED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]':
	       
			Check console output at "${env.JOB_NAME} [${env.BUILD_NUMBER}]" """,
		to: 'niraimani@gmail.com'
}

def notifySuccessful() {
	mail subject: "SUCCESSFUL: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
	    body: """
			SUCCESSFUL: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]':
	       
			Check console output at "${env.JOB_NAME} [${env.BUILD_NUMBER}]" """,
		to: 'niraimani@gmail.com'
}

def notifyFailed() {
	mail subject: "FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
	    body: """
			FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]':
	       
			Check console output at "${env.JOB_NAME} [${env.BUILD_NUMBER}]" """,
		to: 'niraimani@gmail.com'
}
