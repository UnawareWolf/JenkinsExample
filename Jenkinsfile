pipeline {
    agent any 
	environment {
		CURRENT_ENV = 'Jenkins'
	}
    stages {
        stage('Stage 1') {
            steps {
                echo 'Beginning build process from ${CURRENT_ENV}'
				sleep 2
				fileExists 'main.js'
				input 'Do you want to run main.js?'
				bat 'npm install'
				bat 'node main.js'
            }
        }
    }
}