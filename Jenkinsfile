pipeline {
    agent {
        docker {
            image 'maven:3-alpine'
            args '-v /root/.m2:/root/.m2'
        }
    }
   script {
	try {
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
	} catch (err) {
		if(currentBuild.result == null) {
			currentBuild.result = "FAILURE" // sets the ordinal as 4 and boolean to false
		}
		throw err
	} finally {
		step([$class: 'InfluxDbPublisher',
			customData: null,
			customDataMap: null,
			customPrefix: null,
			target: 'local influxDB',
			selectedTarget: 'local_influxDB'
		])
	}
   }
}
