pipeline {
    agent any
    environment {
        MAVEN_HOME = "/var/lib/jenkins/workspace/Demo/apache-maven-3.9.13"
    }
    stages {
        stage('Checkout Code') {
            steps {
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/iservermate/JenkinsNexusDemo.git']])
            }
        }
        stage('Setup Maven') {
            steps {
                script {
                    if (!fileExists("${env.MAVEN_HOME}/bin/mvn")) {
                        sh """
                        wget https://archive.apache.org/dist/maven/maven-3/3.9.13/binaries/apache-maven-3.9.13-bin.tar.gz
                        tar zxf apache-maven-3.9.13-bin.tar.gz
                        """
                    } else {
                        echo "Maven already installed, skipping download."
                    }
                }
            }
        }
        stage('Build') {
            steps {
                dir ('/var/lib/jenkins/workspace/Demo/demoapp'){
                sh "${env.MAVEN_HOME}/bin/mvn clean install"
                }
            }
        }
        stage('Compile Code') {
            steps {
								dir ('/var/lib/jenkins/workspace/Demo/demoapp'){
								sh "${env.MAVEN_HOME}/bin/mvn compile"
								}
            }
        }
        stage('Test Code') {
            steps {
								dir ('/var/lib/jenkins/workspace/Demo/demoapp'){
								sh "${env.MAVEN_HOME}/bin/mvn test"
								}
            }
        }
        stage('Package Code') {
            steps {
								dir ('/var/lib/jenkins/workspace/Demo/demoapp'){
								sh "${env.MAVEN_HOME}/bin/mvn package"
								}
            }
        }
        stage('Nexus Artifact Upload') {
            steps {
								dir ('/var/lib/jenkins/workspace/Demo/demoapp/target'){
								nexusArtifactUploader artifacts: [[artifactId: 'DemoArtifactId', classifier: '', file: 'demoapp-1.0.war', type: 'war']], credentialsId: 'Nexus-Credentials', groupId: 'com.demo', nexusUrl: '192.168.0.74:8081', nexusVersion: 'nexus3', protocol: 'http', repository: 'testrepository', version: '1.0-SNAPSHOT'
								}
            }
        }
        stage('Install Apache Tomcat Server') {
            steps {
                sh '''
                wget https://archive.apache.org/dist/tomcat/tomcat-9/v9.0.73/bin/apache-tomcat-9.0.73.tar.gz
                tar -xzvf apache-tomcat-9.0.73.tar.gz
                chown -R jenkins:jenkins /var/lib/jenkins/workspace/Demo/apache-tomcat-9.0.73
                cp tomcat-users.xml /var/lib/jenkins/workspace/Demo/apache-tomcat-9.0.73/conf/tomcat-users.xml
                cp context.xml /var/lib/jenkins/workspace/Demo/apache-tomcat-9.0.73/conf/context.xml
                sed -i 's/port="8080"/port="8082"/g' /var/lib/jenkins/workspace/Demo/apache-tomcat-9.0.73/conf/server.xml
                '''
				      }
          }
        stage('Deploy Application to Tomcat Server') {
            steps {
                sh '''
                cp /var/lib/jenkins/workspace/Demo/demoapp/target/demoapp-1.0.war /var/lib/jenkins/workspace/Demo/apache-tomcat-9.0.73/webapps/
                sudo -u jenkins /var/lib/jenkins/workspace/Demo/apache-tomcat-9.0.73/bin/startup.sh
                '''
		      }
          }
     }
}
