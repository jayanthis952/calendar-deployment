pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                echo 'Downloading calendar.war...'
                sh 'wget -O calendar.war https://tomcat.apache.org/tomcat-7.0-doc/appdev/sample/sample.war'
            }
        }

        stage('Deploy') {
            steps {
                sshagent(['tomcat-key']) {
                    sh '''
                    echo "Deploying to Tomcat1 (54.90.147.170)..."
                    scp -o StrictHostKeyChecking=no calendar.war ubuntu@18.232.151.172:/opt/tomcat10/webapps/
                    ssh -o StrictHostKeyChecking=no ubuntu@18.232.151.172 "sudo systemctl restart tomcat"

                    echo "Deploying to Tomcat2 (18.234.216.108)..."
                    scp -o StrictHostKeyChecking=no calendar.war ubuntu@3.95.220.209:/opt/tomcat10/webapps/
                    ssh -o StrictHostKeyChecking=no ubuntu@3.95.220.209 "sudo systemctl restart tomcat"
                    '''
                }
            }
        }

        stage('Test') {
            steps {
                echo "Testing deployments..."
                sh 'curl -I http://54.90.147.170:8080/calendar/'
                sh 'curl -I http://18.234.216.108:8080/calendar/'
            }
        }
    }
}
