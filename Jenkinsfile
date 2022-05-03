node{
   stage('SCM Checkout'){
     git 'https://github.com/jai98/Dev-project.git'
   }
   stage('Compile-Package'){

      def mvnHome =  tool name: 'maven3', type: 'maven'   
      sh "${mvnHome}/bin/mvn clean package"
	  sh 'mv target/myweb*.war target/newapp.war'
   }

    stage('SonarQube Analysis'){

      def mvnHome =  tool name: 'maven3', type: 'maven'   
      withSonarQubeEnv('sonar'){
	   sh "${mvnHome}/bin/mvn sonar:sonar"
      }
   }

    stage('Build Docker Imager'){
   sh 'docker build -t surya00/myweb:0.0.2 .'
   }
   stage('Docker Image Push'){
   withCredentials([string(credentialsId: 'dockerPass', variable: 'dockerPassword')]) {
   sh "docker login -u surya00 -p ${dockerPassword}"
    }
   sh 'docker push surya00/myweb:0.0.2'
   }
   stage('Nexus image push in Artifacorty'){
   sh "docker login -u admin -p admin123 65.1.100.3:8087"
   sh "docker tag surya00/myweb:0.0.2 65.1.100.3:8087/jai:1.0.0"
   sh 'docker push 65.1.100.3:8087/jai:1.0.0'
   }
   stage('Remove Previous Container'){
   try{
      sh 'docker rm -f tomcatest'
   }catch(error){
        //  do nothing if there is an exception
   }
    stage('Docker deployment'){
   sh 'docker run -d -p 8090:8080 --name tomcattest surya00/myweb:0.0.2' 
   }
}
}

