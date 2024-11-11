pipeline {
    agent any

    stages {
        // Security Check Stage
        stage('Security Check') {
            steps {
                echo 'Running Vagrant Validation...'
                sh 'vagrant validate'  // Check for Vagrantfile syntax errors
            }
        }

        // Deployment Stage
        stage('Deployment') {
            steps {
                echo 'Starting Vagrant VMs...'
                sh 'vagrant up'  // Start the web_server and db_server VMs
            }
        }

        // Rollback Mechanism
        stage('Rollback') {
            when {
                failure()  // Only runs if any previous stage fails
            }
            steps {
                echo 'Rolling back: Destroying VMs due to failure...'
                sh 'vagrant destroy -f'  // Force destroy the VMs
            }
        }
    }

    post {
        always {
            echo 'Pipeline completed.'
        }
        success {
            echo 'Deployment successful!'
        }
        failure {
            echo 'Deployment failed. See logs for details.'
        }
    }
}
