pipeline {
    agent none 
    stages {
        stage('Build+Test+SonarQube Scan') {
            agent {
                docker {
                    image 'maven:3-alpine'
                    args '-v /root/.m2:/root/.m2'
                }
            }
    	    stages {
                stage('Build') {
                    steps {
                        sh 'mvn -B -DskipTests clean package'
                    }
                }
                stage('Test') {
                    steps {
                        sh 'mvn test'
                    }
                    post {
                        always {
                            junit 'target/surefire-reports/*.xml'
                        }
                    }
                }
                stage('Sonarqube') {
                    environment {
                        scannerHome = tool 'SonarQubeScanner'
                    }
                    steps {
                        withSonarQubeEnv('SonarQube') {
                            sh "${scannerHome}/bin/sonar-scanner -X"
                        }
                        timeout(time: 10, unit: 'MINUTES') {
                            waitForQualityGate abortPipeline: true
                        }
                    }
                }
                stage('Deliver') {
                    steps {
                        sh './jenkins/scripts/deliver.sh'
                    }
                }
    	    } 
	}
    	stage('Export metrics to InfluxDB') {
    	   agent { 
    	      docker { image 'baoannguyen/sonar-exporter' }
    	   }
    	   steps {
                  sh 'python /usr/src/app/sonar-client.py'
    	   }
    	}
    }
}

