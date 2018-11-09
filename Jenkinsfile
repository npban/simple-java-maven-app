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
        }
        stage('Deliver') {
            steps {
                sh './jenkins/scripts/deliver.sh'
            }
        }
    }
	post {
		always {
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
