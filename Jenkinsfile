pipeline {
    agent any

    stages {
        // Security Check Stage
        stage('Security Check') {
            steps {
                echo 'Running Vagrant Validation...'
                dir('D:/iaC_Sparta_Project') {  // Path to the directory containing the Vagrantfile
                    sh 'vagrant validate'  // Check for Vagrantfile syntax errors
                }
            }
        }

        // Deployment Stage
        stage('Deployment') {
            steps {
                echo 'Starting Vagrant VMs...'
                dir('D:/iaC_Sparta_Project') {  // Path to the directory containing the Vagrantfile
                    sh 'vagrant up'  // Start the web_server and db_server VMs
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
            
            // Rollback Mechanism
            echo 'Rolling back: Destroying VMs due to failure...'
            dir('D:/iaC_Sparta_Project') {  // Path to the directory containing the Vagrantfile
                sh 'vagrant destroy -f'  // Force destroy the VMs
            }
        }
    }
}
