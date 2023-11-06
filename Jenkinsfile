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
                        //sh "sudo docker run -d -p 8081:8080 --name jenkcont jenkindock"
                    }
                }
            }
        }
        /* stage('Schedule Stop') {
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
        }*/
           stage('deploy') {
            steps {
                sh 'sudo docker login --username mdabudoc --password-stdin < /var/lib/jenkins/my_password'
                sh 'sudo docker tag jenkindock new_jenkinsdock:v1'
                sh 'sudo docker push jenkindock:latest'
            }
        }
    }
 }
