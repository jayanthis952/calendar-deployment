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
                sshagent(['ubuntu']) {
                    script {
                        // Deploy to Tomcat1
                        echo "Deploying to Tomcat1 (54.197.155.52)..."
                        sh """
                        scp -o StrictHostKeyChecking=no calendar.war ubuntu@54.197.155.52:/tmp/
                        ssh -o StrictHostKeyChecking=no ubuntu@54.197.155.52 'sudo mv /tmp/calendar.war /var/lib/tomcat10/webapps/ && sudo systemctl restart tomcat10'
                        """

                        // Deploy to Tomcat2
                        echo "Deploying to Tomcat2 (100.25.217.178)..."
                        sh """
                        scp -o StrictHostKeyChecking=no calendar.war ubuntu@100.25.217.178:/tmp/
                        ssh -o StrictHostKeyChecking=no ubuntu@100.25.217.178 'sudo mv /tmp/calendar.war /var/lib/tomcat10/webapps/ && sudo systemctl restart tomcat10'
                        """
                    }
                }
            }
        }

        stage('Test') {
            steps {
                echo "Testing deployments..."
                sh 'curl -I http://54.197.155.52:8080/calendar/'
                sh 'curl -I http://100.25.217.178:8080/calendar/'
            }
        }
    }
}
