pipeline {
    agent any
    stages {
        stage('Test Vagrant Version') {
            steps {
                sh 'vagrant --version'  // This will check if vagrant works in the Jenkins pipeline
            }
        }
    }
}
