pipeline {
    agent any
    tools {
        maven 'maven'

    stages {
        stage('Build') {
             steps {
                withSonarQubeEnv('SonarQube') {
                    // Optionally use a Maven environment you've configured already
                    withMaven {
                        sh "mvn clean verify"
                        sh "sudo cp /var/lib/jenkins/workspace/WAR_it_is/target/hello-world-war-1.0.0.war /jenkin_dock"
                        sh "sudo docker build -t jenkindock /jenkin_dock"
                        sh "sudo docker run -d -p 8081:8080 --name jenkcont1 jenkindock"
                    }
                }
            }
        }
    }
}
