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
		stage('Sonarqube analysis') {
	       	withSonarQubeEnv(credentialsId: 'sonarqube') {
	     	   sh 'mvn sonar:sonar -Dsonar.host.url=http://35.238.144.183:9000'
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
	stage('Deploy'){
          withCredentials([file(credentialsId: 'k8s-key', variable: 'K8SCredential')]){
            sh """
            gcloud auth activate-service-account --key-file=${K8SCredential}
            gcloud config set compute/zone us-central1-a
            gcloud config set project awesome-advice-243417
            gcloud container clusters get-credentials casestudy
            kubectl apply -f k8s/
            """
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
