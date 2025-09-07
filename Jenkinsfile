pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                echo "Downloading calendar.war..."
                sh 'wget -O calendar.war https://tomcat.apache.org/tomcat-7.0-doc/appdev/sample/sample.war'
            }
        }

        stage('Deploy') {
            steps {
                sshagent(['tomcat-key']) {
                    sh '''
                    echo "Deploying to Tomcat1 (98.84.151.139)..."
                    scp -o StrictHostKeyChecking=no calendar.war ubuntu@98.84.151.139:/tmp/
                    ssh ubuntu@98.84.151.139 "sudo mv /tmp/calendar.war /opt/tomcat10/webapps/ && sudo systemctl restart tomcat"

                    echo "Deploying to Tomcat2 (34.228.19.124)..."
                    scp -o StrictHostKeyChecking=no calendar.war ubuntu@34.228.19.124:/tmp/
                    ssh ubuntu@34.228.19.124 "sudo mv /tmp/calendar.war /opt/tomcat10/webapps/ && sudo systemctl restart tomcat"
                    '''
                }
            }
        }

        stage('Test') {
            steps {
                echo "Testing deployments..."
                sh 'curl -I http://98.84.151.139:8080/calendar/'
                sh 'curl -I http://34.228.19.124:8080/calendar/'
            }
        }
    }
}
