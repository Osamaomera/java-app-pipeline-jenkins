@Library('https://github.com/Osamaomera/shared_library') _
// @Library('github.com/Osamaomera/shared_library') _
pipeline {

    agent { label 'slave1' }
    
    environment {
        dockerHubCredentialsID	    = 'DockerHub'  		    			// DockerHub credentials ID.
        imageName   		    = 'osayman74/java-app:latest'     			// DockerHub repo/image name.
        OPENSHIFT_SERVER = 'https://api.ocp-training.ivolve-test.com:6443'
        OPENSHIFT_PROJECT = 'osamaayman'
        SERVICE_ACCOUNT_TOKEN = credentials('eyJhbGciOiJSUzI1NiIsImtpZCI6IjRnTW5ETnRFMXQzWFExOHdETzdGNUFsRUI4T3c1TGJIaVNkOWZuUzZiXzAifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJvc2FtYWF5bWFuIiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZWNyZXQubmFtZSI6ImplbmtpbnMtdG9rZW4tcDc0aGQiLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC5uYW1lIjoiamVua2lucyIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VydmljZS1hY2NvdW50LnVpZCI6IjNiYTJiYTE2LTQyYmItNGVkOC1hM2NkLTUwMjdhZTkzODllZiIsInN1YiI6InN5c3RlbTpzZXJ2aWNlYWNjb3VudDpvc2FtYWF5bWFuOmplbmtpbnMifQ.BoNoX5Qo_fucbazqvR5P6KDr6JKCPwJ7TrpMk4mT-eD2ZfmqM_5eECdLAUaOjSs9eRreOUQuPflcVtYMfmsOwwbizMNSAIlrWRNAJNRehFuFbskOctmgB1nXX13HgXenbT2mWAiC3Wx1JW82rRVWLHze7DXPHARXd7MlX9X-CHMqjawaLmhYK1MoWSUJtuE5ENQEg6kwClhkYZBkHqXLRBnUho7pUzpU-669EN0GP2XaAlW7Hd62e3yrZtwcdt5h7xJQilj2a6piEvN_a1aNYcCjqydPtJYKf3A_cyeuc7Dmdjg0u1685n0h2OJgw6JmKx-y2wq9jxelps7ha4J8y7wwJxWtoJ432YcqfF8z6DUaipGx-Ih6qeV8RMu0zDlR12pdgxlL1eDedM4mFskx0zMsj03SuHzqEPtWzTWBVGuGg3z6DO1duI4BvvwMNKMQnpC6vEQDWxYA2KpU_b-0LIRYJNXuZJW-qSJ2BfokrUyZW8A-rN-Cc2JfpySb3A0KlauL17haszfHAkGCDgm84c_XXAtLTFKh6TPGzO7JZy_y0wKUVtGPkYVWAVuqWk-ufs5ABnCFNfKUzTwNhd_cpUqqINTbitdQrjvHZ6fgTxPbqTb5RTIXiVuaHKMl0JdOh_nhH6-8YbEz--VzS6zmhC8Zoe32JIKJ1-KMGSQyWdI')
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
	    
	stage('Deploy on OpenShift cluster') {
            steps {
                script {
        		// Log in to the OpenShift cluster and deploy the image
                    sh """
                        oc login ${OPENSHIFT_SERVER} --token=${SERVICE_ACCOUNT_TOKEN}
                        oc project ${OPENSHIFT_PROJECT}
                        
                        oc set image deployment/your-deployment-name your-container-name=${DOCKER_REGISTRY}/${IMAGE_NAME}:${IMAGE_TAG}
                    """
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
