node{

      def buildNumber = BUILD_NUMBER
    
    // To keep last 5 buil only, old one will be delete
      properties([buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '4', daysToKeepStr: '', numToKeepStr: '4', removeLastBuild: true)), pipelineTriggers([])])
	
       // Github will notify to jenkins once changes/commit is done in github and jenkins starts build automatically 
       properties([pipelineTriggers([githubPush()])])
     
    // Checkout stage
    stage('SCM Checkout'){
        git credentialsId: 'fc94c29f-d9da-45c5-b7e5-b7af1b566cc2', url: 'https://github.com/TheAmitDeokar/java-web-app-docker.git'
    }
    
     // Build stagee
    stage(" Maven Clean Package"){
      def mavenHome =  tool name: "maven3.9.9", type: "maven"
      def mavenCMD = "${mavenHome}/bin/mvn"
      sh "${mavenCMD} clean package"
      
    } 
    
      // Build Docker Image
    stage('Build Docker Image'){
        sh 'docker build -t theamitdeokar/java-web-app .'
    }
    

     // Docker Login and push image
    stage('Docker Login and push image'){
        withCredentials([string(credentialsId: 'DockerHubCredentails', variable: 'DockerHubCredentails')]) {
        sh "docker login -u theamitdeokar -p ${DockerHubCredentails}"
        }
        sh "docker push theamitdeokar/java-web-app:${buildNumber}"
    }


     // Deploy App as Docker Container in Docker Deployment server

   stage('Deploy App as Docker Container in Docker Deployment server'){
        sshagent(['Docker_Dev_Server_SSH']) {
  
          sh "ssh -o StrictHostKeyChecking=no ubuntu@172.31.13.14 docker rm -f javawebappcontainer || true"
     
          sh "ssh -o StrictHostKeyChecking=no ubuntu@172.31.13.14 docker run -d -p 8081:8080 --name javawebappcontainer theamitdeokar/java-web-app:${buildNumber}" 
    }
     }


     // stage('Run Docker Image In Dev Server'){
        
      //  def dockerRun = ' docker run  -d -p 8080:8080 --name java-web-app dockerhandson/java-web-app'
         
       //  sshagent(['DOCKER_SERVER']) {
        //  sh 'ssh -o StrictHostKeyChecking=no ubuntu@172.31.20.72 docker stop java-web-app || true'
        //  sh 'ssh  ubuntu@172.31.20.72 docker rm java-web-app || true'
        //  sh 'ssh  ubuntu@172.31.20.72 docker rmi -f  $(docker images -q) || true'
         // sh "ssh  ubuntu@172.31.20.72 ${dockerRun}" -->
      // }
       
   // }
     
     
}
