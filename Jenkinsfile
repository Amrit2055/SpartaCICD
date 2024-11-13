pipeline {
    agent any

    stages {
        stage('Retrieve Vault Server IP') {
            steps {
                script {
                    // Retrieve host IP address based on the OS
                    def hostIp = ''
                    if (isUnix()) {
                        hostIp = sh(script: "hostname -I | awk '{print \$1}'", returnStdout: true).trim()
                    } else {
                        // PowerShell command for Windows to get the first IPv4 address
                        hostIp = bat(script: 'powershell -command "(Get-NetIPAddress -AddressFamily IPv4 | Select-Object -First 1).IPAddress"', returnStdout: true).trim()
                        hostIp = hostIp.split("\\r?\\n")[2].trim() // Adjusted to remove extra PowerShell formatting
                    }
                    env.VAULT_ADDR = "http://${hostIp}:8200"
                    echo "Vault server IP set to: ${env.VAULT_ADDR}"
                }
            }
        }

        // Security Check Stage
        stage('Security Check') {
            steps {
                echo 'Running Vagrant Validation...'
                script {
                    bat 'vagrant validate'
                }
            }
        }

        // Deployment Stage
        stage('Deploy VMs') {
            steps {
                echo 'Starting Vagrant VMs...'
                script {
                    bat 'vagrant up'
                }
            }
        }

        // Testing Web Server
        stage('Testing Web Server') {
            steps {
                echo 'Testing Web Server Connectivity and Apache Status...'
                script {
                    bat '''vagrant ssh web_server -c "curl -I http://localhost | findstr HTTP/1.1"'''
                }
            }
        }

        // Testing Database Server
        stage('Testing Database Server') {
            steps {
                withCredentials([[$class: 'VaultUsernamePasswordCredentialBinding', credentialsId: 'vault-jenkins', passwordVariable: 'DB_PASSWORD', usernameVariable: 'DB_USERNAME']]) {
                    script {
                        bat """
                        vagrant ssh web_server -c "mysql -h 192.168.57.81 -u %DB_USERNAME% -p%DB_PASSWORD% -e 'SHOW DATABASES;'"
                        """
                    }
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
                bat 'vagrant destroy -f'
            }
        }
    }
}
