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
                    }
                 }
             }
        }
        stage('dockerfile-process') {
             steps {
                 sh "sudo mkdir -p /var/lib/jenkins/workspace/jenkins_main/jenkins_dock"
                 sh "sudo cp /var/lib/jenkins/workspace/jenkins_main/target/hello-world-war-1.0.0.war /var/lib/jenkins/workspace/jenkins_main/jenkins_dock"
                 sh '''
                    echo "FROM tomcat:latest" > /var/lib/jenkins/workspace/jenkins_main/jenkins_dock/Dockerfile
                    echo "COPY *.war /usr/local/tomcat/webapps/" >> /var/lib/jenkins/workspace/jenkins_main/jenkins_dock/Dockerfile
                     '''
             }
        }
         stage('docker-build') {
             steps {
                 sh "sudo docker build -t jenkindock /var/lib/jenkins/workspace/jenkins_main/jenkins_dock/"
             }
         }
         stage('docker-run') {
             steps {
                 sh "sudo docker run -d -p 8081:8080 --name jenkcont jenkindock"
             }
         }
         stage('Schedule Stop') {
            steps {
                script {
                    echo "Scheduled to stop the container in 5 minutes."
                    // Use Jenkins' `timeout` step to initiate a timed block
                    timeout(time: 5, unit:'MINUTES' ) {
                        sh 'sudo docker stop jenkcont'
                        sh 'sudo docker rm jenkcont'
                    }
                }
            }
        }
           stage('deploy') {
            steps {
                sh 'sudo docker login --username mdabudoc --password-stdin < /var/lib/jenkins/my_password'
              }
           }
           stage('docker-tag') {
               steps {
                   sh 'sudo docker tag jenkindock:latest mdabudoc/open:new_jenkindock-v1'
               }
           }
            stage('docker-push') {
                steps {
                   sh 'sudo docker push mdabudoc/open:new_jenkindock-v1'
            }
        }
    }
}


