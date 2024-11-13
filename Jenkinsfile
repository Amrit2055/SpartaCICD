pipeline {
    agent any

    stages {
        stage('Retrieve Vault Server IP') {
            steps {
                script {
                    // Use Windows-compatible command for IP retrieval if on Windows
                    if (isUnix()) {
                        def hostIp = sh(script: """hostname -I | awk '{print \$1}'""", returnStdout: true).trim()
                        env.VAULT_ADDR = "http://${hostIp}:8200"
                    } else {
                        def hostIp = bat(script: 'for /f "tokens=2 delims={}" %%i in (\'powershell -command "(Get-NetIPAddress -AddressFamily IPv4).IPAddress" ^| select -First 1\') do @echo %%i', returnStdout: true).trim()
                        env.VAULT_ADDR = "http://${hostIp}:8200"
                    }
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
                    // Testing database connection with Vault credentials
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
