pipeline {
    agent any
    
    environment {
        dockerHubCredentialsID	    = 'DockerHub'  		    			// DockerHub credentials ID.
        imageName   		    = 'osayman74/nti-java-app'     			// DockerHub repo/image name.
    }
    
    stages {       

        stage('Test') {
            steps {
                script {
                	echo "Running Unit Test..."
			sh 'chmod 744 gradlew'
			sh './gradlew clean test'
        	}
    	    }
	}
	
       
        stage('Build Docker Image') {
            steps {
                script {
        		echo "Building docker image ..."
        		sh "docker build -t ${imageName}:${BUILD_NUMBER} ."
                }
            }
        }

       
        stage('push Docker Image') {
            steps {
                script {
        		echo "pushing docker image ..."
			withCredentials([usernamePassword(credentialsId: "${dockerHubCredentialsID}", usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]){
				sh "docker login -u ${USERNAME} -p ${PASSWORD}"
        		}
			sh "docker push ${imageName}:${BUILD_NUMBER}"
                }
            }
        }
	    
	stage('Deploy on k8s cluster') {
            steps {
                script {
        		echo "deploy on k8s ..."
                }
            }
        }

    }

    post {
        success {
            echo "${JOB_NAME}-${BUILD_NUMBER} pipeline succeeded"
        }
        failure {
            echo "${JOB_NAME}-${BUILD_NUMBER} pipeline failed"
        }
    }
}
