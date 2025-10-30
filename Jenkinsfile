pipeline {
    agent {
        label 'master'
    }

    stages {
        stage('Build') {
            steps {
                bat 'mvn -B -DskipTests clean package'
            }
        }

        stage('Test') { 
            steps {
                bat 'mvn test' 
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml' 
                }
            }
        }

        stage('Sonar-Report') {
            steps {
                script {
                    // Check if SonarQube is reachable before running analysis
                    def sonarUp = false
                    try {
                        def conn = new URL('http://localhost:9000').openConnection()
                        conn.connect()
                        sonarUp = true
                        conn.disconnect()
                    } catch (Exception e) {
                        echo "⚠️ SonarQube not reachable — skipping analysis."
                    }

                    if (sonarUp) {
                        bat 'mvn clean install sonar:sonar -Dsonar.host.url=http://localhost:9000 -Dsonar.analysis.mode=publish'
                    } else {
                        echo "Skipping SonarQube stage since server is not running."
                    }
                }
            }
        }
    }
}
