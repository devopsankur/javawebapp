node{
  
    stage('SCM Checkout'){
        git url: 'https://github.com/devopsankur/javawebapp.git',branch: 'master'
    }
    
    stage('Build'){
      def mavenHome =  tool name: "maven-3", type: "maven"
      def mavenCMD = "${mavenHome}/bin/mvn"
      sh "${mavenCMD} clean package"
      
    } 
    
    
    stage('Create Docker Image'){
        sh 'docker build -t ankur7827/java-web-app .'
    }
    
    stage('Push Docker Image'){
        withCredentials([usernamePassword(credentialsId: 'docker-hub-cred', passwordVariable: 'Docker_Hub_Pwd', usernameVariable: 'Docker_Hub_Username')]) {
        sh "docker login -u ${Docker_Hub_Username} -p ${Docker_Hub_Pwd}"
        }
        sh 'docker push ankur7827/java-web-app'
     }
     
      stage('Run Docker Image In Tomcat Server'){
        
        def dockerRun = ' docker run  -d -p 8080:8080 --name java-web-app ankur7827/java-web-app'
         
         sshagent(['b2071599-8b72-483b-be71-53447052b323']) {
          sh 'ssh -o StrictHostKeyChecking=no ec2-user@172.31.30.86 docker stop java-web-app || true'
          sh 'ssh  ec2-user@172.31.30.86 docker rm java-web-app || true'
          sh 'ssh  ec2-user@172.31.30.86 docker rmi -f  $(docker images -q) || true'
          sh "ssh  ec2-user@172.31.30.86 ${dockerRun}"
       }
       
    }
     
     
}