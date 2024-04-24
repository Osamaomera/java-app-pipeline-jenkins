# Jenkins Pipeline 

> This document provides an overview of the Jenkins pipeline for building Dockerized java applications and push its image to DockerHub


## Pipeline Overview

The Jenkins pipeline follows these stages to build, push Docker images to dockerhub :

1.  **Test:** Test java application.
2.  **Build and Push to DockerHub:** Build image of applicatio.
3.  **Push to DockerHub:** Push image to DockerHub.


## Environment Variables

The following environment variables are used in the Jenkins pipeline:

- `imageName`: dockerhub_repo/image_name.
- `dockerHubCredentialsID`: dockerhub username and password to login.
  

## Pipeline Steps

### Test:

```
 stage('Test') {
            steps {
                script {
                	echo "Running Unit Test..."
			sh 'chmod 744 gradlew'
			sh './gradlew clean test'
        	}
    	    }
	}
```

### Build Docker image:

```
stage('Build Docker Image') {
            steps {
                script {
        		echo "Building docker image ..."
        		sh "docker build -t ${imageName}:${BUILD_NUMBER} ."
                }
            }
        }
```

### push Docker Image:

```
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
```

### Post-Build Actions
In case of pipeline success or failure, the following messages will be displayed:

```
post {
        success {
            echo "${JOB_NAME}-${BUILD_NUMBER} pipeline succeeded"
        }
        failure {
            echo "${JOB_NAME}-${BUILD_NUMBER} pipeline failed"
        }
    }
```
----
### - Successfully the image built.
![]()

### - Successfully the image pushed to repo of DockerHub.
![]()

### - Successfully run the pipeline
![]()

#####################################################
![]()
---

