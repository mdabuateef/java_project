pipeline {
    agent any
    tools {
        maven 'Maven'
         }
    stages {
        stage('Build') {
             steps {
                withSonarQubeEnv('SonarQube') {
                    // Optionally use a Maven environment you've configured already
                    withMaven {
                        sh "mvn clean verify"
                        sh "sudo mkdir -p /var/lib/jenkins/workspace/jenkins_main/jenkins_dock"
                        sh "sudo cp /var/lib/jenkins/workspace/jenkins_main/target/hello-world-war-1.0.0.war /var/lib/jenkins/workspace/jenkins_main/jenkins_dock"
                        sh '''
                            echo "FROM tomcat:latest" > /var/lib/jenkins/workspace/jenkins_main/jenkins_dock/Dockerfile
                            echo "COPY *.war /usr/local/tomcat/webapps/" >> /var/lib/jenkins/workspace/jenkins_main/jenkins_dock/Dockerfile
                            '''
                        sh "sudo docker build -t jenkindock /var/lib/jenkins/workspace/jenkins_main/jenkins_dock/"
                        sh "sudo docker run -d -p 8081:8080 --name jenkcont jenkindock"
                    }
                }
            }
        }
    }
}
