# Get Source code > Automate Build - Deploy steps.

mvn compile	#target/classes
mvn test	#target/surefire-reports
mvn package	#target/example.war


#INSTALL NEXUS
mkdir nexus-data; chmod -R 200 nexus-data
>$docker run -d -p 8081:8081 --name nexus --rm --network cicd-network -v /Users/mkazmi/Documents/JenkinsNexusPipline/nexus-data:/nexus-data sonatype/nexus3
http://localhost:8081
cat /Users/mkazmi/Documents/JenkinsNexusPipeLine/nexus-data/admin.password
{ 5c6e8f01-c232-47d0-a4f7-4beea2f7617d% }
password is: password

Repo I created: mven-releases

#INSTALL JENMKINS
mkdir jenkins_home
mkdir jenkins_hosts
touch jenkins_hosts/hosts
>$ docker run --name jenkins4nexus --rm --network cicd-network -h jenkins -d -p 8080:8080 -p 50000:50000 -v $(pwd)/jenkins_home:/var/jenkins_home  jenkins/jenkins:jdk21
http://localhost:8081
cat /var/jenkins_home/secrets/initialAdminPassword
{ 8978b26fe03748f38c2ce2f667fb90ee }
password is: password
Install Plugin: 
  - Nexus Artifact Uploader 
  - Pipeline  
  - Docker Pipeline 
  - Git


#TOMCAT
mkdir tomcat_webapps
>$ docker run -d --rm -p 8888:8080 --name tomcat --rm --network cicd-network -v /Users/mkazmi/Documents/JenkinsNexusPipline/webapps:/usr/local/tomcat/webapps tomcat:9.0
http://localhost:8888/sample/