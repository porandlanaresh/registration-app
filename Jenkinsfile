pipeline {
   agent { label 'jenkins-agent'}
   tools { 
       jdk 'java17'
       maven 'maven3'
    }
   environment {
           APPNAME = "register-app-pipeline"
           RELEASE = "1.0.0"
           DOCKER_USER = "nrshub"
           DOCKER_PASS = 'dockerhub'
           IMAGE_NAME = "${DOCKER_USER}"  +  "/"  + "${APPNAME}"
           IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"
        } 
    stages{
        stage("Cleanup Workspace"){
          steps {
            cleanWs()
        }
     }
         stage("checkout from SCM"){
             steps {
                git branch: 'main', credentialsId: 'github', url: 'https://github.com/porandlanaresh/register-app'
          }        
       }
          stage("Build Application"){
              steps {
               sh "mvn clean package"
           }
        }
          stage("Test Application"){
               steps {
                sh "mvn test"
            }
        }

           stage("Build & Push Docker Image") {
              steps {
                  script { 
                      docker.withRegistry('',DOCKER_PASS) {
                      sh 'export DOCKER_BUILDKIT=1 && docker build -t nrshub/register-app-pipeline .'

                     }
                       docker.withRegistry('',DOCKER_PASS) {
                           docker_image.push("${IMAGE_TAG}")
                           docker_image.push('latest')
                     }
                  }
               }
         }
  }
}
