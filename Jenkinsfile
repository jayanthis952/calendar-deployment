pipeline {
    agent { label 'master' }

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
                    echo "Deploying to Tomcat1 (18.232.151.172)..."
                    scp -o StrictHostKeyChecking=no calendar.war ubuntu@98.84.151.139:/tmp/
                    ssh ubuntu@98.84.151.139 "sudo mv /tmp/calendar.war /opt/tomcat10/webapps/ && sudo systemctl restart tomcat"

                    echo "Deploying to Tomcat2 (3.95.220.209)..."
                    scp -o StrictHostKeyChecking=no calendar.war ubuntu@34.228.19.124:/tmp/
                    ssh ubuntu@34.228.19.124 "sudo mv /tmp/calendar.war /opt/tomcat10/webapps/ && sudo systemctl restart tomcat"
                    '''
                }
            }
        }

        stage('Test') {
            steps {
                echo "Testing deployments..."
                sh 'curl -I http://18.232.151.172:8080/calendar/'
                sh 'curl -I http://3.95.220.209:8080/calendar/'
            }
        }
    }
}
