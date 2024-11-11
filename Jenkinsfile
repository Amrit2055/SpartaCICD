pipeline {
    agent any

    stages {
        // Security Check Stage
        stage('Security Check') {
            steps {
                echo 'Running Vagrant Validation...'
                script {
                    sh 'vagrant validate'  // Run the shell command outside the sandbox
                }
            }
        }

        // Deployment Stage
        stage('Deployment') {
            steps {
                echo 'Starting Vagrant VMs...'
                script {
                    sh 'vagrant up'  // Run the shell command outside the sandbox
                }
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
            script {
                sh 'vagrant destroy -f'  // Rollback mechanism outside the sandbox
            }
        }
    }
}
