pipeline {
    agent any 
    stages {
        stage('Stage 1') {
            steps {
                echo 'Beginning build process'
				sleep 2
				fileExists 'main.js'
				input 'Do you want to run main.js?'
				sh 'node main.js'
            }
        }
    }
}