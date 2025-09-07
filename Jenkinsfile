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
                        // Deploy to Tomcat1 (systemd-managed)
                        echo "Deploying to Tomcat1 (54.197.155.52)..."
                        sh """
                            scp -o StrictHostKeyChecking=no calendar.war ubuntu@54.197.155.52:/tmp/
                            ssh -o StrictHostKeyChecking=no ubuntu@54.197.155.52 '
                                sudo mv /tmp/calendar.war /var/lib/tomcat10/webapps/ &&
                                sudo systemctl restart tomcat10
                            '
                        """

                        // Deploy to Tomcat2 (script-managed)
                        echo "Deploying to Tomcat2 (100.27.187.254)..."
                        sh """
                            scp -o StrictHostKeyChecking=no calendar.war ubuntu@100.27.187.254:/tmp/
                            ssh -o StrictHostKeyChecking=no ubuntu@100.27.187.254 '
                                sudo mv /tmp/calendar.war /opt/tomcat10/webapps/ &&
                                sudo /opt/tomcat10/bin/shutdown.sh || true &&
                                sudo /opt/tomcat10/bin/startup.sh
                            '
                        """
                    }
                }
            }
        }

        stage('Test') {
            steps {
                echo "Testing deployments..."
                script {
                    // Tomcat1
                    def response1 = sh(script: "curl -Is http://54.197.155.52:8080/calendar/ | head -n 1", returnStdout: true).trim()
                    echo "Tomcat1 Response: ${response1}"

                    // Tomcat2
                    def response2 = sh(script: "curl -Is http://100.27.187.254:8080/calendar/ | head -n 1", returnStdout: true).trim()
                    echo "Tomcat2 Response: ${response2}"

                    if (!response1.contains("200") || !response2.contains("200")) {
                        error("Deployment test failed on one or both servers!")
                    }
                }
            }
        }
    }
}
