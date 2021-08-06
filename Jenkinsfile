// prerequisites: a nodejs app must be deployed inside a kubernetes cluster
// TODO: look for all instances of [] and replace all instances of 
//       the 'variables' with actual values 
// variables:
//      https://github.com/Tilakj/events-app-web-server.git
//      main
//      dtcaugust2021-223
//      web-server-image
//      cnd-events-cluster 
//      us-central1-c
//      the following values can be found in the yaml:
//      demo-ui
//      demo-ui (in the template/spec section of the deployment)

pipeline {
    agent any 
    stages {
        stage('Stage 1 - sourcecode') {
            steps {
                echo 'Retrieving source from github' 
                git branch: 'main',
                    url: 'https://github.com/Tilakj/events-app-web-server.git'
                echo 'Did we get the source?' 
                sh 'ls -a'
            }
        }
        stage('Stage 2 - versions') {
            steps {
                echo 'workspace and versions' 
                sh 'echo $WORKSPACE'
                sh 'gcloud version'
                sh 'node -v'
                sh 'npm -v'
        
            }
        }        
         stage('Stage 3 - dependencies') {
            environment {
                PORT = 8081
            }
            steps {
                echo 'install dependencies' 
                sh 'npm install'
                echo 'Run tests'
                sh 'npm test'
        
            }
        }        
         stage('Stage 4 - build') {
            steps {
                echo "build id = ${env.BUILD_ID}"
                echo 'Tests passed on to build Docker container'
                sh "gcloud builds submit -t gcr.io/dtcaugust2021-223/web-server-image:v2.${env.BUILD_ID} ."
            }
        }        
         stage('Stage 5- publish') {
            steps {
                echo 'Get cluster credentials'
                sh 'gcloud container clusters get-credentials cnd-events-cluster --zone us-central1-c --project dtcaugust2021-223'
                echo 'Update the image'
                echo "gcr.io/dtcaugust2021-223/web-server-image:2.${env.BUILD_ID}"
                sh "kubectl set image deployment/demo-ui demo-ui=gcr.io/dtcaugust2021-223/web-server-image:v2.${env.BUILD_ID} --record"
            }
        }
    }
}

