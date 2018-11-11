pipeline {
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
        stage('Deliver') {
            steps {
                sh './jenkins/scripts/deliver.sh'
            }
        }
    }
	post {
		success {
			script {
				currentBuild.result = "SUCCESS"
				step([$class: 'InfluxDbPublisher',
					target: 'local_influxDB'
				])
			}
		}
		failure {
			script {
				currentBuild.result = "FAILURE"
				step([$class: 'InfluxDbPublisher',
					target: 'local_influxDB'
				])
			}
		}
		unstable {
			script {
				currentBuild.result = "UNSTABLE"
				step([$class: 'InfluxDbPublisher',
					target: 'local_influxDB'
				])
			}
		}
		aborted {
			script {
				currentBuild.result = "ABORTED"
				step([$class: 'InfluxDbPublisher',
					target: 'local_influxDB'
				])
			}
		}
	}
}
