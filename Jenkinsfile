/*def stage(String name, Closure cl) {
    echo "Stage: ${name}"
    try {
        cl()
    } catch (Exception e) {
        // I needed to save failed stage and message for parent pipeline job
        // so I saved them in environment variables, otherwise it can be saved
        // in global variables
        if (!env.FAILED_STAGE) {
            env.FAILED_STAGE = name
            env.FAILED_MESSAGE = e.getMessage()
        }
    }
}*/

pipeline {
  agent any 
    stages{
      stage("Docker Build"){
        steps{
          script {
           last_started = env.STAGE_NAME
          sh "docker build -t docker ."   
        }  
      }
      }
      stage("Run Docker image"){
        steps{
            script {
           last_started = env.STAGE_NAME
          sh "docker run --name nginx -itd -p 8088:80 docker:latest"   
        } 
        }
      }  
      stage("Pushing to docker hub"){
        steps{
          withCredentials([usernamePassword(credentialsId: 'dockerhub', passwordVariable: 'pass', usernameVariable: 'userId')]) {
              script {
            last_started = env.STAGE_NAME
            sh 'docker login -u ${userId} -p ${pass}'
            sh "docker commit nginx revathismart/docker:latest"
            sh "docker push revathismart/docker:latest"   
          }
        }  
        }
      }
   }
  post {
    success {
        echo 'Successfully completed '    
    }
    failure {
      //echo "Error caught${env.err}"
       echo "Build failed at $last_started"
       //echo "Failed stage: ${env.FAILED_STAGE}"
       //echo "Error message: ${env.FAILED_MESSAGE}"
    }  
  }
}
