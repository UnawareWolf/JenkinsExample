pipeline {
    agent any 
	environment {
		CURRENT_ENV = 'Jenkins'
	}
    stages {
        stage('Stage 1') {
            steps {
				withCredentials([string(credentialsId: '7bebdd26-2649-45c3-bed2-289cd7da2d49', variable: 'Password')]) {
					echo 'Beginning build process from ${Password}'
					sleep 2
					fileExists 'main.js'
					input 'Do you want to run main.js?'
					bat 'npm install'
					bat 'node main.js'
				}
            }
        }
    }
}